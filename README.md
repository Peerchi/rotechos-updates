# RotechOS

RotechOS es una interfaz web y un conjunto de macros para una fresadora CNC con
**RepRapFirmware 3.5.4** sobre una **BTT Scylla V1.0**. La interfaz se sirve desde la
tarjeta SD de la placa y se abre en un navegador; no hay que instalar un programa en el
ordenador.

En un ordenador o una tableta se muestra como **RotechOS**. En un teléfono se muestra como
**RotechOS Pendant**. Es la misma máquina y la misma instalación, con una vista adaptada a
la pantalla.

Este repositorio es el canal público de descargas y actualizaciones.

## Elige tu caso

| Si ya tienes RotechOS | Si vas a montar tu propia máquina |
|---|---|
| Actualiza desde la propia interfaz: **Instalar ahora** con internet o **Instalar desde un ZIP** sin internet. | Instala el firmware de la placa si hace falta, copia RotechOS a una tarjeta SD y calibra tu máquina. |

La guía completa está en **[GUIA_INSTALACION.md](GUIA_INSTALACION.md)**.

## Descargar una versión

Abre **[la última versión](https://github.com/Peerchi/rotechos-updates/releases/latest)** y
entra en la sección **Assets**. En la release v2026.10 los ficheros se llaman así:

| Asset de la release | Cuándo descargarlo |
|---|---|
| `rotechos-v2026.10.zip` | Siempre. Es el paquete de RotechOS para actualizar desde la interfaz o preparar una tarjeta SD nueva. |
| `SHA256SUMS.txt` | Recomendado. Permite comprobar que la descarga no ha quedado dañada. |
| `firmware.bin` | Solo para una Scylla nueva o para una placa que no tenga RepRapFirmware 3.5.4. Es el firmware de la placa, no el del módulo WiFi. |
| `MANUAL_USUARIO_ROTECHOS.pdf` | Opcional. Manual de usuario completo. |

No descargues **Source code (zip)** para instalar RotechOS: no es el paquete de la tarjeta SD.
Cada release contiene una SD completa, así que quien vaya varias versiones atrás instala
la última.

Para verificar los assets, descarga el ZIP, `SHA256SUMS.txt` y, si lo necesitas,
`firmware.bin`. En macOS o Linux, desde la carpeta que contiene los ficheros, ejecuta:

```text
shasum -a 256 -c SHA256SUMS.txt
```

La línea del ZIP y, si lo has descargado, la de `firmware.bin` deben terminar en `OK`.
El PDF no forma parte de `SHA256SUMS.txt`. Si no has descargado `firmware.bin`, comprueba
solo el hash de la línea del ZIP comparando su salida con `SHA256SUMS.txt`.

## Actualizar una instalación que ya funciona

Antes de actualizar, detén la máquina y asegúrate de que no hay ningún trabajo en marcha.
Descarga un respaldo desde **CONFIGURACIÓN → RESPALDO**. El respaldo no incluye
`sys/wifi-passwords.g`, que contiene tus credenciales WiFi; tenlas a mano para volver a
introducirlas si fuera necesario.

### Con internet: «Instalar ahora»

1. Abre la interfaz y espera al aviso de nueva versión, o pulsa **Buscar actualizaciones**
   en **CONFIGURACIÓN → ACTUALIZAR (OTA)**.
2. Pulsa **Instalar ahora** y confirma la versión que se muestra.
3. No apagues la máquina ni cierres la página mientras se descarga y se escribe la tarjeta.
4. La placa se reinicia al terminar. Antes de hacer HOME, mueve cada eje un poco con JOG y
   comprueba el sentido del movimiento con la mano en la seta de emergencia.

La interfaz descarga el paquete publicado, comprueba su SHA-256 y pide confirmación antes de
escribir. Si el dispositivo no tiene internet, usa el método siguiente.

### Sin internet: «Instalar desde un ZIP»

1. En otro dispositivo con internet, descarga de la release el ZIP exacto, por ejemplo
   `rotechos-v2026.10.zip`. No lo descomprimas.
2. Pasa ese ZIP al teléfono, tableta u ordenador que está conectado a la máquina, aunque esa
   conexión sea únicamente a la red WiFi **RotechOS**.
3. Entra en **CONFIGURACIÓN → ACTUALIZAR (OTA)**, pulsa **Instalar desde un ZIP** y elige el
   ZIP sin descomprimir.
4. Confirma. No apagues la máquina ni cierres la página hasta que termine y se reinicie.

Este instalador comprueba el ZIP y escribe los ficheros de `ROTECHOS/sys/`,
`ROTECHOS/macros/`, `ROTECHOS/licenses/` y `ROTECHOS/www/`. Conserva `config.g`,
`config-machine.g`, `config-override.g`, `user-daemon.g` y `wifi-passwords.g` de tu máquina.
No usa `firmware.bin` ni vuelve a grabar el firmware del módulo WiFi durante una actualización.

Si una subida se interrumpe, la tarjeta puede quedar con parte de los ficheros nuevos. No hay
vuelta atrás automática: repite la instalación; si la interfaz no arranca, sigue la sección
correspondiente de la guía.

Las versiones anteriores a la 2026-10-09 no tienen estos dos botones. En ese caso, sigue la
guía para actualizar manualmente.

## Montar una máquina propia

RotechOS está planteado para **BTT Scylla V1.0 + RepRapFirmware 3.5.4**. Antes de encender
una máquina nueva, quien la integra debe resolver el cableado, la seta de emergencia, los
finales de carrera, el husillo, la sonda y la calibración de esa unidad. La interfaz no
sustituye las protecciones físicas ni proporciona la calibración de otra máquina.

Para una instalación nueva, sigue la sección de instalación de la guía en este orden:

1. Si la placa no tiene RepRapFirmware 3.5.4, usa el asset `firmware.bin` de la release y
   grábalo primero en la tarjeta SD. Al arrancar, el bootloader debe renombrarlo a
   `FIRMWARE.CUR`; apaga la placa antes de continuar. Si la release no incluye ese asset, la
   guía enlaza el port de RepRapFirmware para placas STM32 de TeamGloomy.
2. Descomprime `rotechos-v2026.10.zip` en el ordenador. Copia el **contenido** de su carpeta
   `ROTECHOS/` a la raíz de la tarjeta SD. Deben quedar directamente `www/`, `sys/`,
   `macros/`, `firmware/` y `licenses/`.
3. El ZIP incluye `ROTECHOS/firmware/WiFiModule_esp32.bin`. Solo hace falta grabarlo con
   `M997 S1`, desde una conexión USB serie a 115200 baudios, si una placa nueva no crea
   ninguna red WiFi.
4. En el primer arranque, la red propia se llama **RotechOS** y usa la clave de fábrica
   **`rotechos`**. Abre `http://192.168.1.1`. Es una clave conocida: conviene cambiarla antes
   de dejar esa red accesible a otras personas y, mientras siga siendo la de fábrica, usar la
   red solo con dispositivos de confianza.
5. Completa **PRIMER ARRANQUE** y la calibración de tu unidad. Si partes de un fichero, el ZIP
   trae `rotechos-sys/config-machine.g.ejemplo`: léelo entero, cambia sus valores y cópialo
   como `0:/sys/config-machine.g`. No copies una calibración de otra máquina.
6. Antes del primer HOME, mueve cada eje un poco con JOG y comprueba el sentido. La **sonda Z**
   para medir la altura es un **pulsador de contacto mecánico**, no una sonda inductiva.

El husillo tiene dos variantes documentadas: un **VFD controlado por firmware**, con rango de
6000 a 24000 RPM en esta configuración, o un **Makita RT0701C manual**, cuyo dial e
interruptor se manejan físicamente. RotechOS no puede encender ni apagar el Makita desde la
interfaz.

## Avisos importantes

- No hay marcado CE. Es un kit DIY / retrofit; quien integra la máquina final debe asumir sus
  obligaciones y comprobaciones.
- No existe reanudación automática de un trabajo después de un corte de luz. Hay que volver a
  referenciar la máquina y relanzar el trabajo desde el principio.
- El PIN HTTP es opcional: una instalación nueva puede abrirse sin PIN. Se define, cambia o
  quita en **CONFIGURACIÓN → PIN HTTP**. El PIN protege el acceso a la interfaz, no sustituye
  la seta de emergencia, no cifra la conexión y no es una protección física.
- No dejes la máquina mecanizando sin vigilancia. En la primera puesta en marcha y tras cada
  cambio de configuración: sin material, sin fresa dentro de la pieza y con la mano en la
  seta de emergencia.
- La seta de emergencia debe estar cableada en la alimentación. Un botón o una orden que
  dependa del software no la sustituye.

La versión v2026.10 sigue marcada en sus notas como candidata pendiente de validación con la
máquina encendida. Lee `NOTAS_DE_VERSION.md` antes de ponerla a trabajar.

## Manual de usuario

Consulta el **[Manual de usuario de RotechOS](https://github.com/Peerchi/rotechos-updates/releases/latest/download/MANUAL_USUARIO_ROTECHOS.pdf)**
(69 páginas) para la puesta en marcha, operación, configuración, mantenimiento y resolución
de problemas.

## Licencia y atribuciones

RotechOS se distribuye bajo **GPL-3.0**. El ZIP incluye el texto de la licencia y los avisos
en `ROTECHOS/licenses/gpl-v3.txt`, `ROTECHOS/licenses/NOTICE.txt` y
`ROTECHOS/licenses/source.txt`; la interfaz, macros y configuración se entregan como texto
legible.

La interfaz tiene su origen en **Duet Web Control** (Duet3D Ltd) y RotechOS es una obra
derivada bajo GPL-3.0. La máquina usa **RepRapFirmware 3.5.4**. Consulta sus proyectos de
código fuente en [Duet3D/RepRapFirmware](https://github.com/Duet3D/RepRapFirmware) y
[gloomyandy/RepRapFirmware](https://github.com/gloomyandy/RepRapFirmware). RepRapFirmware y
Duet son marcas de Duet3D Ltd; RotechOS no está afiliado a Duet3D.

## Si algo va mal

Abre una incidencia contando la placa, la versión de RotechOS, lo que esperabas y lo que
ocurrió. Si la interfaz abre, **CONFIGURACIÓN → IDENTIDAD → COPIAR SOPORTE** reúne los datos
útiles para diagnosticarlo.
