# Manual de Usuario · Lavadero Autos & Motos

Bienvenido a tu sistema de gestión para lavadero de autos y motos. Esta guía explica paso a paso cómo configurar tu local, administrar turnos, fidelizar clientes y resguardar tu información.

---

## 1. Primeros Pasos: Personalizá tu Lavadero
Tocá el ícono de **Configuración (rueda dentada)** arriba a la derecha:

1. **Pestaña Local & Cobro:**
   - **Logo del Lavadero:** Tocá **Subir Logo** y elegí una imagen de tu celular o compu.
   - **Nombre del Lavadero y Dirección:** Se muestran en tu panel, en el Turnero Online y en las tarjetas de fidelidad.
   - **Código Único (Link Turnero):** Es tu identificador propio en la nube (por ejemplo `el-rayo`). Sirve para que tus turnos nunca se mezclen con los de otros lavaderos que usen la app.
   - **WhatsApp y Datos de Cobro QR:** Cargá tu número de WhatsApp y tu **Alias / CVU** y **Titular** de Mercado Pago o banco para cobrar sin comisiones.

2. **Pestaña Horarios & Boxes:**
   - **Vehículos a la vez (Boxes):** Por defecto viene en `1 vehículo por turno` (al reservarse, el horario pasa automáticamente a gris `Ocupado`). Si tenés 2 o más lavadores/puestos en simultáneo, elegí `2`, `3` o los que correspondan.
   - **Duración del turno:** Cada `30`, `45`, `60` o `90` minutos.
   - **Horarios por día:** Configurá **Lunes a Domingo** uno por uno. Podés marcar un día como cerrado (ej. Domingo) y activar **Horario cortado** en los días que cerrás al mediodía (ej. `08:30 a 13:00` y `15:00 a 19:30`).

3. **Pestaña Fidelidad & WhatsApp:**
   - Definí cuántos lavados completan la tarjeta (ej. `6`), cuál es el premio (ej. `1 Lavado Completo Gratis`) y **a los cuántos días caducan los sellos sin visitas** (ej. `60 días`).
   - Cada vez que el cliente viene antes de esa fecha, el plazo se renueva automáticamente.
   - Editá tus plantillas de **Bienvenida** y **Vehículo Listo para Retirar** (sin emojis para evitar símbolos rotos en WhatsApp).

4. **Pestaña Seguridad PIN (Opcional):**
   - Podés activar un **PIN de Dueño (4 a 6 números)** y una **Clave Maestra de Rescate** (offline, sin internet).
   - Al activarlo, la carga rápida diaria sigue ágil, pero entrar a **Configuración**, editar **Tarifas**, **Editar/Borrar clientes** o **Restaurar copias** pedirá el PIN.
   - Si alguien ingresa 5 veces mal el PIN, el acceso se bloquea por **120 segundos** y queda registrado en el historial de alertas.

---

## 2. Tarifas y Servicios
Tocá el botón **Tarifas** en la pantalla principal:
- Podés modificar precios en pesos argentinos (`ARS $`) directamente sobre cada fila.
- Podés agregar servicios nuevos para **Moto**, **Auto**, **Camioneta** o **Todos**.
- Con la casilla **"Mostrar precios en el Turnero Online"** elegís si querés que tus clientes vean las tarifas al reservar o prefieras mantenerlas ocultas.

---

## 3. Mensaje de Bienvenida y Enlaces por WhatsApp
Tocá el botón verde **Bienvenida** en la barra superior:
- Ingresá el nombre y WhatsApp del cliente (o dejá el teléfono vacío para elegir el contacto al abrir WhatsApp).
- El mensaje ya incluye automáticamente el link de tu **Turnero Online**, tu **Alias**, las reglas de **Fidelidad** y opcionalmente tu **lista de tarifas**.

---

## 4. Turnero Online y Control Manual (`Agenda`)
- **Compartir con clientes:** Tocá **Copiar Link del Turnero** o **QR Turnero** (ideal para pegar en el mostrador).
- Cuando un cliente reserva un horario (y se llenan los boxes disponibles de esa hora), el botón aparece automáticamente en **gris como `Ocupado`** para los demás clientes.
- **Control manual del dueño (`Agenda`):**
  - Tocá **Agenda** arriba para ver todas las reservas del día.
  - Podés **Cerrar día** completo (feriados o lluvia), **Bloquear** un horario puntual, **Liberar** un lugar o **Agendar** manualmente a alguien que te llamó por teléfono.
  - Con el botón **+ Ficha** pasás los datos del turno directo a tu lista de clientes para registrarle el lavado.

---

## 5. Registro de Lavados, Fidelidad y Cobro QR
En cada tarjeta de cliente:
- **+ Lavado Hoy:** Elegí el servicio, verificá el importe y confirmá. Suma `+1 sello` y renueva el contador de vigencia. Si tenía deuda pendiente, podés dejarlo cargado en cuenta.
- **WhatsApp Listo:** Abre WhatsApp con el mensaje listo avisando que el vehículo se puede retirar, el monto y el link a su **Pase Digital** actualizado.
- **Cobro QR:** Generá un código QR al instante con tu Alias/CVU (y monto opcional) para que el cliente escanee desde su billetera virtual. Si tenía deuda pendiente, aparece un botón rojo destacado para cobrarla y saldarla en 1 toque.

---

## 6. Copia de Seguridad (Google Drive y Archivo Local)
- La app guarda todo en tu dispositivo al instante (funciona incluso sin internet para la gestión de mostrador) y sincroniza copias automáticas con la carpeta **"Copia de seguridad Lavaderos"** en Google Drive cuando detecta Wi-Fi o datos.
- Tocando el ícono de la **nube** arriba a la derecha podés forzar una copia en Drive en el acto, descargar un archivo `.json`, exportar una planilla `.csv` para Excel o restaurar un respaldo previo.
- También podés pegar tu propio webhook de Google Apps Script en la sección avanzada si preferís enviar las copias a tu propia cuenta de Google Drive.

---

## 7. Instalar como App en el Celular (PWA)
- **Android (Chrome):** Abrí el menú de los tres puntos y tocá **"Instalar aplicación"** o **"Agregar a la pantalla principal"**.
- **iPhone (Safari):** Tocá el botón **Compartir** y elegí **"Agregar a inicio"**.
