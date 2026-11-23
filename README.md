# OpenCode Remote (Android)

Cliente Android nativo para controlar sesiones de **OpenCode V2** desde el móvil.
Interfaz en español, tema claro/oscuro y streaming en vivo mediante SSE.

## Versión 0.2.0

- **Principal**: conexión manual por URL y token (funciona en cualquier dispositivo).
- Lista de sesiones con búsqueda y estado en vivo.
- Chat con respuestas del agente en streaming (texto y razonamiento).
- Tarjetas de herramientas con entrada, salida y errores.
- Permisos del agente: permitir una vez, permitir siempre o rechazar.
- Detener la ejecución en curso.
- Cambiar de agente y de modelo.
- Ejecutar comandos de OpenCode (`/init`, `/review`, `/goal`, …).
- Conexión por enlace de emparejamiento (`opencode pair`) o manual (URL + token).
- Credenciales guardadas cifradas con `EncryptedSharedPreferences`.

## Requisitos

- OpenCode **V2** en el ordenador (probado con **v2.3.2**).
- Android 8.0 (API 26) o superior.
- Móvil y ordenador en la misma red, o un túnel (OpenTunnel, Tailscale, SSH…).

## Conectar con el ordenador

### 1. Misma Wi-Fi (LAN) — método recomendado

En el ordenador:

```sh
opencode service set hostname 0.0.0.0
opencode service start
```

Averigua la IP local (`ip addr` o `ipconfig`). Después genera un enlace:

```sh
opencode pair --url http://TU_IP:49374
```

Pega el enlace en la app y pulsa **Emparejar y conectar**. Si el enlace contiene
`127.0.0.1`, escribe la IP real del PC en el campo **IP del PC (opcional)**.

> El puerto puede variar. Usa el que muestre `opencode service status`.

### 2. Desde cualquier sitio (remoto)

```sh
opencode pair --remote
```

Esto crea un túnel de OpenTunnel y expone el servidor a Internet. Pega el enlace
en la app. La primera vez puede pedir confirmación; en modo no interactivo usa
`opencode pair --remote --yes`.

### 3. Manual

Introduce la URL base y la contraseña o token:

- URL: `http://192.168.1.10:49374`
- Contraseña: el token de emparejamiento o la contraseña del servidor.
  Puedes consultarla con `opencode service get password`.

La autenticación usa HTTP Basic con usuario `opencode`.

## Usar el escaneo QR (versión 0.2.0)

Si tu teléfono tiene cámara y Google Play Services for AR:

1. En el PC, ejecuta `opencode pair` — aparecerá un código y un QR en la terminal.
2. En la app móvil, toca **Escanear QR y conectar**.
3. Se abrirá el escáner de cámara del sistema.
4. Cuadrar el QR que aparece en la pantalla del ordenador.
5. La app intentará conectar primero por la red LAN y, si no es alcanzable, por
   el túnel remoto (OpenTunnel).
6. Si el QR apunta a `127.0.0.1`, aparecerá un campo para escribir la IP real del
   PC.
7. Si el escaneo falla o el sistema no pide permiso de cámara, usa el método
   manual a continuación.

**Si el escaneo no funciona o aparece solo texto:** el QR contiene un código y
varias URLs. La app intentará conectarse automáticamente; si no logra conectar,
usa el método manual que se describe a continuación.

## Conexión manual (siempre funciona)

Si el escaneo QR no funciona o prefieres pegar el enlace:

1. En el PC, ejecuta `opencode pair` y copia el enlace que aparece (ej.,
   `http://10.0.2.2:49374/auth/connect/ABC123`).
2. En la app móvil, toca **URL y contraseña**.
3. Pega la URL en el campo **URL del servidor**.
4. El campo **Contraseña o token** se completará automáticamente con el token
   generado durante el emparejamiento.
5. Pulsa **Conectar con URL y contraseña**.

> Este método funciona en **cualquier** dispositivo Android, independientemente
> de tener cámara o no.

## Uso

- **Sesiones**: toca una tarjeta para abrir el chat. El punto verde indica que la
  sesión está ejecutándose.
- **Nueva**: crea una sesión con título y directorio opcionales.
- **Chat**: escribe abajo y pulsa enviar. Mientras el agente trabaja verás el
  indicador «Ejecutando…» y el botón **Detener**.
- **Menú ⋮**: cambiar agente, cambiar modelo, ejecutar comando, actualizar o
  desconectar.
- **Permisos**: cuando el agente necesite permiso aparecerá un diálogo con los
  recursos afectados.

## Seguridad

- No expongas el puerto de OpenCode directamente a Internet. Usa
  `opencode pair --remote`, OpenTunnel, Tailscale o un túnel SSH.
- El token se guarda cifrado en el dispositivo y se borra al desconectar.
- La app permite HTTP en claro para funcionar en la red local. Para conexiones
  remotas usa siempre HTTPS.
- Concede el permiso de cámara solo si confías en la fuente del QR.

## Limitaciones de esta versión

- No incluye escaneo de QR garantizado en todos los dispositivos (requiere
  cámara y Google Play Services for AR). El método **manual** funciona siempre.
- No permite adjuntar archivos, imágenes ni audio.
- No archiva, renombra ni elimina sesiones.
- La lista de sesiones carga las 50 más recientes (sin paginación).
- No hay notificaciones en segundo plano.

---

**Generado con OpenCode V2** · Código fuente en `/home/pedro/opencode-mobile`
