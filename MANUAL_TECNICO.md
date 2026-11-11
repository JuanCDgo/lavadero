# Manual Técnico · Lavadero Autos & Motos

**Stack:** HTML5, Tailwind CSS (CDN), Vanilla JavaScript (ES6+), PWA (`manifest.webmanifest` + `sw.js`), `LocalStorage`, API KeyValue (`keyvalue.immanuel.co`), Google Apps Script Webhook (Drive), `QRCode.js`, `Canvas Confetti`.  
**Despliegue:** GitHub Pages (`https://juancdgo.github.io/lavadero/`).

---

## 1. Arquitectura General

Single-Page Application (SPA) offline-first diseñada para operar sin servidor backend propietario:
- **Persistencia local:** Toda la base operativa (configuración, tarifas, clientes, vehículos, historial de lavados y log de seguridad) reside en `localStorage` del dispositivo.
- **Turnero colaborativo multi-lavadero:** Utiliza un esquema de claves aislado por `businessId` sobre el servicio KV `https://keyvalue.immanuel.co/api/KeyVal` con fragmentación automática en Base64URL (`KV_CHUNK_SIZE = 180`) para respetar el límite `maxUrlSegmentLength` (~255 caracteres) de IIS/ASP.NET.
- **Copia de seguridad externa:** Sincronización automática debounced (`1800ms`) y al evento `online` hacia Google Drive vía `POST` (`mode: 'no-cors'`, `Content-Type: text/plain;charset=utf-8`), más exportación/importación `.json` y `.csv`.

### Estructura de Archivos
```text
mini_app-lavadero_de_autos_motos/
├── index.html             # SPA completa (Admin + Turnero Público + Pase Fidelidad + Modales <dialog>)
├── manifest.webmanifest   # Manifiesto PWA instalable
├── sw.js                  # Service Worker (Network-first con fallback offline para assets locales)
├── icon.svg               # Ícono vectorial PWA / Favicon
├── qrcode.min.js          # Generador QR local (con fallback a QuickChart.io)
├── MANUAL_USUARIO.md      # Guía operativa del dueño/encargado
└── MANUAL_TECNICO.md      # Especificación técnica y esquemas
```

---

## 2. Esquema de Persistencia (`localStorage`)

| Clave | Contenido |
|---|---|
| `lavadero_settings_v1` | Identidad (`businessId`, `businessName`, `businessAddress`, `businessLogo`), datos de cobro (`paymentAlias`, `paymentHolder`), `showPublicPrices`, configuración de turnero (`bookingEnabled`, `bookingCapacity`, `bookingSlotMinutes`, `weeklySchedule`), fidelidad (`loyaltyEnabled`, `loyaltyTarget`, `loyaltyReward`, `loyaltyExpiryDays`), plantillas WhatsApp y hashes de seguridad (`pinEnabled`, `ownerPinHash`, `masterKeyHash`). |
| `lavadero_services_v1` | Array de servicios `{ id, name, type, price, active }`. |
| `lavadero_clients_v1` | Array de clientes `{ id, name, phone, vehicleType, vehicleModel, plate, lastWashDate, lastService, lastWashPrice, stamps, totalWashes, debt, notes }`. |
| `lavadero_washes_v1` | Últimos 500 lavados `{ id, clientId, clientName, vehicle, service, price, date }`. |
| `lavadero_pin_lock_v1` | Estado anti-fuerza bruta `{ fails, lockedUntil }`. |
| `lavadero_sec_log_v1` | Últimas 50 alertas de seguridad con timestamp `America/Argentina/Buenos_Aires`. |

---

## 3. Protocolo del Turnero en Nube (Multi-Lavadero y Boxes)

### Aislamiento por Negocio (`businessId`)
Cada instancia genera o define un `businessId` único (`lav-xxxxx` o slug personalizado) utilizado como namespace:
- **`lav_cfg_<businessId>_n` / `_0..k`:** Configuración pública del local (nombre, dirección, WhatsApp, `showPublicPrices`, `bookingCapacity`, `bookingSlotMinutes`, `weeklySchedule`, y `services`). Nunca expone clientes ni saldos.
- **`lav_logo_<businessId>_n` / `_0..k`:** Logo comprimido en JPEG (`<= 96px`, calidad `0.62`), almacenado en clave separada para no sobrecargar publicaciones de tarifas u horarios.
- **`lav_d_<businessId>_YYYYMMDD_n` / `_0..k`:** Estado de reservas del día:
  ```json
  {
    "closed": false,
    "blockedHours": ["11:00"],
    "slots": {
      "09:00": [
        { "id": "b_123", "n": "Carlos", "p": "54911...", "v": "Auto", "m": "Cronos", "pat": "AB123CD", "s": "Completo", "pr": 16000, "t": "client" }
      ]
    }
  }
  ```

### Control de Capacidad Simultánea y Concurrencia
- Un horario `h` se considera **Ocupado** (`disabled`, estilo gris `🔒 Ocupado`) cuando `dayObj.closed === true`, `blockedHours.includes(h)`, el horario ya pasó en el día corriente (`GMT-3`), o `slots[h].length >= bookingCapacity`.
- Antes de confirmar una reserva en `submitClientBooking()`, se ejecuta un `fetchCloudDay()` atómico para prevenir condiciones de carrera entre dos clientes simultáneos.

---

## 4. Caducidad de Fidelidad (Opción A · Inactividad)

Calculada por `getLoyaltyStatus(client, cfg)` usando fechas calendario exactas en huso `America/Argentina/Buenos_Aires`:
- `daysElapsed = diffDaysDateStr(client.lastWashDate, getArgTodayStr())`
- `daysLeft = loyaltyExpiryDays - daysElapsed`
- Si `daysLeft <= 0`: estado `expired` (sellos efectivos = `0`; al registrar el próximo lavado inicia un ciclo nuevo desde `1` sello).
- Si `1 <= daysLeft <= 10`: estado `expiring` (alerta visual y filtro **"Sellos por vencer"**).
- Cada nuevo lavado actualiza `lastWashDate = today`, reiniciando la vigencia completa de los sellos acumulados.

---

## 5. Seguridad, WhatsApp y Estándares Web

- **PIN de Dueño y Rescate Offline:** Almacenado mediante `hashSecret()` (SHA-256 con salt vía `crypto.subtle` en HTTPS/localhost). Tras 5 intentos erróneos aplica bloqueo temporal de `120.000 ms` con cuenta regresiva en vivo y registro en `lavadero_sec_log_v1`. La Clave Maestra permite restablecer el PIN offline.
- **WhatsApp Cero Rombos:** `cleanWhatsAppText()` elimina rangos UTF-8 de emojis de 4 bytes antes de construir URLs con `encodeURIComponent()` hacia `https://api.whatsapp.com/send`.
- **UI Moderna y Prevención XSS:** Modales implementados sobre `<dialog>` nativo (`showModal()`, `overscroll-behavior: contain`, fallback de `closedby="any"` para Safari), validación diferida `:user-invalid` + fallback `WeakMap`, y escape estricto (`escapeHTML()` + validación de `data:image/` / `https://`) en todo contenido dinámico.
