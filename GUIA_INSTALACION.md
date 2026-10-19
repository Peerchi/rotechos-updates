# RotechOS: actualizar o instalar desde cero

Esta guía es para quien tiene la máquina delante y no necesita ser programador. RotechOS
está planteado para una **BTT Scylla V1.0** con **RepRapFirmware 3.5.4**.
La v2026.10 está publicada, pero conserva validaciones pendientes con la máquina
encendida: lee sus notas antes de ponerla a trabajar.

> Regla de seguridad: cada vez que la máquina vaya a moverse después de instalar, actualizar
> o cambiar una configuración, hazlo sin material, sin fresa dentro de la pieza y con la mano
> en la seta roja de emergencia.

## Primero: elige el camino

- **Ya tienes RotechOS:** ve a [Actualizar una instalación existente](#1-actualizar-una-instalación-existente).
- **Vas a montar tu propia máquina:** ve a [Instalación nueva](#2-instalación-nueva).

Si solo quieres consultar cómo se usa la interfaz, tienes el
[Manual de usuario de RotechOS](https://github.com/Peerchi/rotechos-updates/releases/latest/download/MANUAL_USUARIO_ROTECHOS.pdf).

## Descargar la release correcta

Abre **[la última release](https://github.com/Peerchi/rotechos-updates/releases/latest)** y usa
la sección **Assets**. Para la v2026.10, el paquete se llama exactamente
`rotechos-v2026.10.zip`.

Descarga según tu caso:

| Fichero | Uso |
|---|---|
| `rotechos-v2026.10.zip` | Siempre. Sirve para actualizar desde la interfaz y para preparar una SD nueva. |
| `SHA256SUMS.txt` | Recomendado para comprobar la descarga. |
| `firmware.bin` | Solo para una placa nueva o una placa que no tenga RepRapFirmware 3.5.4. Es firmware de la placa y va separado del ZIP. |
| `MANUAL_USUARIO_ROTECHOS.pdf` | Opcional. Manual de usuario. No se copia a la SD. |

No uses **Source code (zip)**. No es el paquete instalable.

### Contenido real de `rotechos-v2026.10.zip`

El listado comprobado con `unzip -l` contiene 208 entradas. La estructura que debes reconocer
es esta:

```text
README.md
NOTAS_DE_VERSION.md
GUIA_INSTALACION.md
rotechos-sys/config-machine.g.ejemplo

ROTECHOS/firmware/WiFiModule_esp32.bin
ROTECHOS/licenses/NOTICE.txt
ROTECHOS/licenses/gpl-v3.txt
ROTECHOS/licenses/source.txt
ROTECHOS/macros/*.g
ROTECHOS/sys/*.g
ROTECHOS/sys/guards/check_homing_safe.g
ROTECHOS/sys/VERSION
ROTECHOS/sys/board.txt
ROTECHOS/www/index.html
ROTECHOS/www/index.html.gz
ROTECHOS/www/manifest.json
ROTECHOS/www/sw.js
ROTECHOS/www/sw.js.gz
ROTECHOS/www/css/ (ficheros .css y .css.gz)
ROTECHOS/www/data/school-tips.json
ROTECHOS/www/icons/icon-192.png
ROTECHOS/www/icons/icon-512.png
ROTECHOS/www/js/ (ficheros .js y .js.gz)
```

Los tres puntos representan subcarpetas y ficheros que sí están dentro de esas carpetas; no
son nombres que tengas que crear. El listado completo y la comprobación de nombres se hace
con `unzip -l`.

Estos ficheros **no están dentro del ZIP**: `firmware.bin`, `SHA256SUMS.txt` y
`MANUAL_USUARIO_ROTECHOS.pdf`. Son assets independientes de la release.

Para comprobar los hashes en macOS o Linux, descarga los ficheros que vayas a usar y ejecuta
en esa carpeta:

```text
shasum -a 256 -c SHA256SUMS.txt
```

La línea del ZIP y, si lo has descargado, la de `firmware.bin` deben mostrar `OK`. El PDF no
forma parte de `SHA256SUMS.txt`. Si solo has descargado el ZIP, compara manualmente la salida
de `shasum -a 256 rotechos-v2026.10.zip` con la línea correspondiente del fichero de sumas.

## 1. Actualizar una instalación existente

### Antes de actualizar

1. Detén la máquina. No actualices con un trabajo en marcha ni en pausa.
2. Entra en **CONFIGURACIÓN → RESPALDO** y pulsa **Descargar backup.zip**.
3. Lee `NOTAS_DE_VERSION.md` de la release, en especial cualquier apartado «Si vienes de…».
4. Ten a mano tus credenciales WiFi. El respaldo no incluye `sys/wifi-passwords.g`, porque
   contiene la contraseña de tu red.

### A. Con internet: «Instalar ahora»

1. Abre la interfaz. Si hay una versión nueva, aparecerá un aviso con **Instalar ahora**.
   También puedes entrar en **CONFIGURACIÓN → ACTUALIZAR (OTA)** y pulsar **Buscar
   actualizaciones**.
2. Pulsa **Instalar ahora** y confirma la versión.
3. Espera a que termine. No apagues la máquina ni cierres la página.
4. La interfaz descarga el paquete publicado, comprueba su SHA-256, escribe los ficheros y
   reinicia la placa. No se instala nada sin tu confirmación.
5. Cuando vuelva la interfaz, recarga la página si todavía muestra la versión anterior.
   Antes de hacer HOME, mueve cada eje un poco con JOG y comprueba el sentido con la mano en
   la seta.

Este método necesita que el dispositivo que abre la interfaz tenga internet. Si estás
conectado únicamente a la red RotechOS, usa el método B.

### B. Sin internet: «Instalar desde un ZIP»

1. En otro dispositivo con internet, descarga el asset `rotechos-v2026.10.zip` de la release.
2. Pasa el ZIP al dispositivo que está conectado a la máquina. No lo descomprimas.
3. Abre **CONFIGURACIÓN → ACTUALIZAR (OTA)**.
4. Pulsa **Instalar desde un ZIP** y selecciona el ZIP intacto.
5. Lee la confirmación y pulsa **Instalar**. Durante la instalación, no apagues la máquina ni
   cierres la página.
6. La placa se reinicia. Comprueba la versión y, antes de hacer HOME, prueba el sentido de
   cada eje con JOG y la mano en la seta.

Este instalador valida el ZIP y solo toma de él estas cuatro zonas:

- `ROTECHOS/sys/`
- `ROTECHOS/macros/`
- `ROTECHOS/licenses/`
- `ROTECHOS/www/`

Conserva los ficheros propios de la máquina: `config.g`, `config-machine.g`,
`config-override.g`, `user-daemon.g` y `wifi-passwords.g`. Tampoco usa `firmware.bin` ni
actualiza el firmware del módulo WiFi.

Si la subida se corta, la tarjeta puede quedar con parte de los ficheros nuevos. **No hay
vuelta atrás automática.** Repite la instalación completa. Si la interfaz deja de cargar,
ve [Si la interfaz no carga](#5-si-la-interfaz-no-carga).

### Si tu versión es anterior a la 2026-10-09

Las versiones anteriores no tienen los botones **Instalar ahora** e **Instalar desde un ZIP**.
Para esa primera actualización, usa la [actualización manual con la tarjeta SD](#4-actualización-manual-con-la-tarjeta-sd)
o sigue exactamente las notas de la release que tengas instalada.

## 2. Instalación nueva

Esta sección es para una BTT Scylla V1.0 nueva o para una máquina que todavía no tenga la
tarjeta SD preparada. La calibración y el cableado son propios de cada máquina.

### Paso 0: firmware de la placa, solo si hace falta

1. Descarga `firmware.bin` desde los assets de la release si la Scylla es nueva o no tiene
   RepRapFirmware 3.5.4.
2. Apaga la máquina y copia `firmware.bin` a la **raíz** de la tarjeta SD, no dentro de
   `ROTECHOS/`.
3. Inserta la tarjeta y enciende la placa. El bootloader debe grabarlo y renombrarlo a
   `FIRMWARE.CUR`.
4. Apaga la placa y comprueba que el nombre ha cambiado antes de seguir.

Si la release no incluye `firmware.bin`, usa el port de RepRapFirmware para STM32 de
[TeamGloomy](https://github.com/gloomyandy/RepRapFirmware/releases), comprobando que la
versión es la que necesita RotechOS. La lista de placas está en
[TeamGloomy: placas compatibles](https://teamgloomy.github.io/supported_boards.html).

No confundas este fichero con `ROTECHOS/firmware/WiFiModule_esp32.bin`: son firmwares
distintos y tienen pasos distintos.

### Paso 1: copiar RotechOS a la SD

1. Apaga la máquina y saca la tarjeta SD.
2. Descomprime `rotechos-v2026.10.zip` en el ordenador.
3. Abre la carpeta `ROTECHOS/` del ZIP.
4. Copia su **contenido** a la raíz de la tarjeta SD. No copies la carpeta `ROTECHOS/` como
   una carpeta adicional.

En la raíz de la SD deben quedar directamente:

```text
www/
sys/
macros/
firmware/
licenses/
```

Los ficheros `README.md`, `NOTAS_DE_VERSION.md`, `GUIA_INSTALACION.md` y la carpeta
`rotechos-sys/` se quedan en el ordenador; no forman parte de esas cinco carpetas de la SD.

### Paso 2: firmware del módulo WiFi, solo si no aparece ninguna red

El ZIP incluye `ROTECHOS/firmware/WiFiModule_esp32.bin`. Si la placa nueva no crea ninguna
red WiFi:

1. Conecta el ordenador a la placa por USB.
2. Abre un terminal serie a **115200 baudios**.
3. Envía `M997 S1` una sola vez.
4. Cuando termine, vuelve a usar la red desde la interfaz.

Si la red RotechOS ya aparece, no repitas este paso.

### Paso 3: primer arranque y red

1. Vuelve a poner la tarjeta SD y enciende.
2. La máquina crea su propia red WiFi **RotechOS** con una clave única para esa máquina. Léela en la etiqueta entregada con ella o, si no la tienes, en CONFIGURACIÓN → Red.
3. Conecta el móvil, tableta u ordenador a esa red y abre `http://192.168.1.1`.
4. Si quieres usar la red WiFi del taller, entra en **CONFIGURACIÓN → Red** y configúrala.
   Si la máquina no consigue conectarse, la red propia RotechOS vuelve a estar disponible.

La clave se guarda además en `ROTECHOS/sys/ap-key.g`, que queda fuera de la OTA. Si pierdes
la etiqueta, apaga la máquina, saca la SD, lee ese fichero con un lector y vuelve a colocarla.
También puedes usar CONFIGURACIÓN → Red → «Generar otra clave», confirmar y volver a imprimir
la etiqueta. Si el fichero falta o está corrupto, la máquina conserva temporalmente la clave
conocida `rotechos` como fallback y la interfaz la indicará.

El PIN HTTP es opcional: una instalación nueva abre la interfaz sin PIN. Puedes definirlo,
cambiarlo o quitarlo en **CONFIGURACIÓN → PIN HTTP**. El PIN solo controla el acceso a la
interfaz web; no cifra la conexión ni sustituye una seta de emergencia cableada.

### Paso 4: calibración y primera prueba

1. Completa el asistente **PRIMER ARRANQUE**.
2. Calibra los valores de tu máquina: pasos por mm, corrientes, área de trabajo, finales de
   carrera, husillo y sonda. No copies la calibración de otra máquina.
3. Si partes de un fichero, usa `rotechos-sys/config-machine.g.ejemplo` del ZIP. Léelo
   entero, cambia los valores de ejemplo y cópialo en la SD como `0:/sys/config-machine.g`.
4. Sin material y sin fresa dentro de la pieza, mueve cada eje un poco con JOG. Comprueba que
   cada eje va en la dirección que indica la pantalla.
5. Si un eje se mueve al revés, corrígelo en **CONFIGURACIÓN → MOTORES** y reinicia cuando la
   interfaz lo pida. No hagas HOME con un eje invertido.

La **sonda Z** de medida es un **pulsador de contacto mecánico**. No es una sonda inductiva.
Los sensores usados como finales de carrera pueden ser elementos distintos; no los confundas
con la sonda de altura.

El husillo tiene dos variantes:

- **VFD controlado por firmware:** rango configurado de 6000 a 24000 RPM.
- **Makita RT0701C manual:** velocidad en su dial e interruptor físico; RotechOS no puede
  encenderlo ni apagarlo desde la interfaz.

## 3. Qué conserva una actualización

El instalador de la interfaz conserva los ficheros de tu máquina, entre ellos:

- `sys/config-machine.g`: calibración de esa máquina.
- `sys/config.g`: mapeo y configuración de motores.
- `sys/config-override.g` y `sys/user-daemon.g`, si existen.
- `sys/wifi-passwords.g`: credenciales de tu WiFi.
- `sys/ap-key.g`: clave propia del punto de acceso de esta máquina; la OTA no la toca.

Por eso una actualización normal no instala el `firmware.bin` de la placa ni el firmware del
módulo WiFi. Si las notas de una release piden modificar `config.g`, hazlo solo siguiendo esas
notas y verifica después el sentido de los ejes.

## 4. Actualización manual con la tarjeta SD

Usa esta ruta principalmente si tu versión es anterior a la 2026-10-09 y todavía no ofrece
los botones de instalación.

1. Detén el trabajo, descarga un respaldo y apaga la máquina.
2. Saca la tarjeta SD y guarda una copia de sus carpetas `www/`, `sys/` y `macros/` en el
   ordenador.
3. Del ZIP nuevo, sustituye `www/` y `macros/` completas.
4. En `sys/`, copia los ficheros nuevos encima de los existentes. El ZIP no trae
   `config-machine.g`, `config-override.g` ni `wifi-passwords.g`, así que esos ficheros
   propios no se sustituyen.
5. El ZIP sí trae `config.g`. Si cambiaste motores, finales de carrera o pines, lee las notas
   de la release antes de sobrescribirlo.
6. Vuelve a poner la tarjeta, enciende y comprueba la versión. Antes de HOME, prueba cada eje
   con JOG y la mano en la seta.

## 5. Si la interfaz no carga

Ve por orden y detente en cuanto funcione:

1. Recarga la página una o dos veces. Tras una actualización, el navegador puede tener en
   memoria la versión anterior.
2. Si estás conectado a la red RotechOS, prueba `http://192.168.1.1`. Si usas la WiFi del
   taller, prueba la dirección IP de la máquina.
3. Borra los datos del sitio en el navegador y vuelve a abrirlo.
4. Prueba desde otro teléfono u ordenador.
5. Si sigue sin cargar, apaga, saca la tarjeta y comprueba que existe `www/index.html`.
   Si falta o la actualización quedó a medias, vuelve a copiar `www/` del ZIP con la tarjeta
   (la opción manual anterior).
6. Si no se resuelve, abre una incidencia con la versión, los pasos que hiciste y lo que ves
   en pantalla. Si la interfaz llega a abrir, usa **CONFIGURACIÓN → IDENTIDAD → COPIAR
   SOPORTE**.

La máquina no depende de la interfaz para la parada de emergencia: la seta roja debe estar
cableada en serie con la alimentación y es la que se debe usar ante un peligro. No dejes la
máquina mecanizando sin vigilancia.

## 6. Volver atrás

1. Consigue el ZIP de la versión anterior y reinstálalo con **Instalar desde un ZIP** o con la
   tarjeta SD.
2. Si necesitas recuperar la configuración, restaura el `backup.zip` desde
   **CONFIGURACIÓN → RESPALDO**. Restaurar sustituye la configuración actual por la del
   respaldo; cualquier cambio posterior al respaldo se pierde.
3. El respaldo no contiene tus credenciales WiFi. Vuelve a configurarlas si hace falta.
4. Tras volver atrás, prueba cada eje con JOG y la mano en la seta antes de hacer HOME.

## 7. Limitaciones, licencia y atribuciones

- No hay marcado CE: es un kit DIY / retrofit y quien integra la máquina final debe asumir
  sus obligaciones.
- RotechOS no reanuda automáticamente un trabajo después de un corte de luz. Hay que volver a
  referenciar y relanzar el trabajo desde el principio.
- RotechOS se distribuye bajo **GPL-3.0**. El ZIP contiene `ROTECHOS/licenses/gpl-v3.txt`,
  `NOTICE.txt` y `source.txt`.
- La interfaz tiene su origen en **Duet Web Control** (Duet3D Ltd). El firmware usado es
  **RepRapFirmware 3.5.4**. Sus proyectos de código fuente están en
  [Duet3D/RepRapFirmware](https://github.com/Duet3D/RepRapFirmware) y
  [gloomyandy/RepRapFirmware](https://github.com/gloomyandy/RepRapFirmware).
- RepRapFirmware y Duet son marcas de Duet3D Ltd; RotechOS no está afiliado a Duet3D.
