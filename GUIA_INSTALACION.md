# RotechOS — instalar, actualizar y volver atrás

Guía para quien tiene la máquina delante y no es programador. Vale para la versión que
venga con ella (candidata de lanzamiento, pendiente de validación en banco: lee
`NOTAS_DE_VERSION.md`, dentro del ZIP, o las notas de la página de la versión).

> **Regla que manda sobre todo lo demás:** cada vez que la máquina vaya a moverse después de
> instalar o actualizar, hazlo **sin material, sin fresa dentro de la pieza y con la mano en
> la seta roja de emergencia.**

---

## Qué hay en el ZIP de la versión

```
ROTECHOS/
  www/        La interfaz. Va a 0:/www/ de la tarjeta SD.
  sys/        Lo que la placa lee al arrancar. Va a 0:/sys/.
  macros/     Macros de usuario (sonda, aparcar, bordeador…). Van a 0:/macros/.
  firmware/   Firmware del módulo WiFi (WiFiModule_esp32.bin). Va a 0:/firmware/.
  licenses/   Licencia y avisos. Va a 0:/licenses/.
rotechos-sys/config-machine.g.ejemplo   Plantilla de TU calibración (no va a la SD tal cual)
README.md, NOTAS_DE_VERSION.md, GUIA_INSTALACION.md   (para leer, no van a la SD)
SHA256SUMS.txt                                        (junto al ZIP: huella para comprobarlo)
firmware.bin                                          (junto al ZIP, si la versión lo trae:
                                                       RepRapFirmware para una placa nueva)
```

**Lo que el ZIP no trae nunca, a propósito:**

| Fichero | Qué es | Por qué no viene |
|---|---|---|
| `sys/config-machine.g` | **Tu calibración**: pasos/mm, corrientes, área, sonda, PIN | Es de tu máquina; una actualización no puede pisarla |
| `sys/config-override.g` | Ajustes locales | Igual |
| `sys/wifi-passwords.g` | Nombre y contraseña de tu WiFi | Son tuyos y van en texto plano |

---

## 1 · Instalación en una tarjeta nueva

Requisito: la placa **BTT Scylla V1.0 lleva RepRapFirmware 3.5.4** (RotechOS se desarrolla
sobre esa versión). Si la placa es nueva o lleva otra, empieza por el paso 0.

0. **Solo si hay que poner el firmware de la placa.** Descarga `firmware.bin` de la página de
   la versión (si no lo trae, el de la Scylla sale del port de RepRapFirmware para STM32 de
   TeamGloomy: https://github.com/gloomyandy/RepRapFirmware/releases). Cópialo en la **raíz**
   de la tarjeta, ponla en la placa y enciéndela. El bootloader lo graba y lo renombra a
   `FIRMWARE.CUR`: si al sacar la tarjeta ves ese nombre, ha ido bien. Vuelve a apagar.
1. **Apaga la máquina** y saca la tarjeta SD de la placa.
2. Descomprime el ZIP en tu ordenador (doble clic).
3. Copia el **contenido** de la carpeta `ROTECHOS/` a la raíz de la tarjeta: tienen que quedar
   `www/`, `sys/`, `macros/`, `firmware/` y `licenses/` directamente en la tarjeta.
4. Vuelve a poner la tarjeta y enciende.
5. **Red.** De fábrica la máquina arranca directamente en su propia red, llamada
   **RotechOS** (clave de fábrica `rotechos`). Conéctate a ella y abre `http://192.168.1.1`.
   Ya puedes trabajar así, sin WiFi en el taller.
   - **Si prefieres usar tu WiFi:** CONFIGURACIÓN → Red; escribe tu red y su clave y pulsa
     «Conectar a esta red». Desde entonces arrancará en tu WiFi. Si no conecta, la red
     RotechOS vuelve sola en un par de minutos.
   - **Para volver a la red propia:** CONFIGURACIÓN → Red → «Trabajar siempre con la red
     propia RotechOS».

   **Kit con placa nueva:** el módulo WiFi de una Scylla recién comprada puede venir sin su
   firmware, y entonces no crea ninguna red. Se graba una sola vez: conecta el ordenador a la
   placa por USB, abre un terminal serie (115200 baudios) y envía `M997 S1` (usa
   `firmware/WiFiModule_esp32.bin`, que ya viene en el ZIP). Después, todo lo de la red se
   hace desde la interfaz.
6. Al abrir la interfaz por primera vez sale un asistente. **Síguelo entero**, en orden. Sin
   `config-machine.g` la máquina arranca con **valores de reserva prudentes** (poca velocidad y
   un área de trabajo pequeña) que no son los tuyos: el asistente de **PRIMER ARRANQUE** te
   lleva a medirlos y guardarlos. Si prefieres partir de un fichero, rellena
   `rotechos-sys/config-machine.g.ejemplo` (léelo entero: sus números son de ejemplo) y
   cópialo a la tarjeta como `sys/config-machine.g`.
7. Antes del primer HOME: mueve cada eje un poco con el JOG y comprueba que va hacia donde
   dice la pantalla. Si un eje va al revés, se corrige en CONFIGURACIÓN → MOTORES (inversión
   de giro). **Nunca hagas HOME con un eje invertido.**

---

## 2 · Actualizar a una versión nueva

### Antes de empezar (siempre)

1. La máquina **parada**, sin ningún trabajo en marcha.
2. **Copia de seguridad:** CONFIGURACIÓN → **RESPALDO** → *Descargar backup.zip*. Guarda el
   fichero en tu ordenador. Es lo que te permite volver atrás.
3. Lee `NOTAS_DE_VERSION.md` de la versión nueva, sobre todo el apartado «Si vienes de…».

### Opción A · Desde la interfaz (OTA), sin sacar la tarjeta

1. Descomprime el ZIP en tu ordenador. **No arrastres el `.zip`**: la interfaz lo rechaza.
2. Abre CONFIGURACIÓN → **ACTUALIZAR (OTA)**. Deja el DESTINO en *Auto (extensión)*.
3. Arrastra a la zona de subida **todos los ficheros de `ROTECHOS/www/`**, incluidos los
   `.gz` y los de sus subcarpetas `css/`, `js/`, `data/` e `icons/`. La interfaz pone cada
   uno en su sitio por el nombre; en la cola ves a dónde va cada fichero.
4. Arrastra los ficheros de **`ROTECHOS/sys/`**.
   - `config.g` **te pregunta** antes de entrar en la cola, porque lleva el mapeo de tus
     motores. Súbelo solo si las notas de versión lo piden (y sigue lo que dicen).
   - `config-machine.g` y las credenciales WiFi no se aceptan nunca: es a propósito.
   - `VERSION` y `board.txt` preguntan «Destino no claro»: acepta *Subir a 0:/sys*.
   - No subas los ficheros de la subcarpeta `sys/guards/` por la OTA: irían a `0:/sys/` en vez
     de a `0:/sys/guards/`. Si una versión los cambia, cópialos con la tarjeta (opción B).
5. Pulsa **Subir archivos** y confirma. Al terminar verás *Archivos web actualizados —
   recargando* y la pantalla se recarga sola.
6. Las **macros** van en una segunda subida, **con la cola vacía**: elige **DESTINO =
   0:/macros**, arrastra los ficheros de `ROTECHOS/macros/` y sube. (Sin cambiar el destino,
   una macro se iría a `sys/`. Y cambiar el DESTINO afecta a todo lo que ya esté en la cola.)
   Al acabar, vuelve a dejar el DESTINO en *Auto (extensión)*.
7. Si has subido ficheros de `sys/`, **reinicia la placa** (o apágala y enciéndela).
8. Comprueba la versión: aparece en el pie de la pantalla y en la cabecera de ACTUALIZAR (OTA).
   Si ves la anterior, recarga la página una vez más: la primera carga puede venir de la
   memoria del navegador.

**Si la subida se corta a mitad** (se va la WiFi, se apaga algo): **no hay vuelta atrás
automática.** La tarjeta queda con parte de los ficheros nuevos y la interfaz lo dice
(«SD PARCIAL»). Repite la subida completa. Si la máquina no arranca bien, sigue la sección 3.

### Opción B · Con la tarjeta en el ordenador

1. Apaga la máquina y saca la tarjeta.
2. Sustituye `www/` y `macros/` enteras por las del ZIP.
3. En `sys/`, copia los ficheros del ZIP **encima** de los que hay. Como el ZIP no trae
   `config-machine.g` ni tus credenciales, esos se quedan como estaban. **`config.g` sí viene
   en el ZIP**: si alguna vez cambiaste MOTORES, FINAL DE CARRERA o CONTROLADORA (pines), lee
   antes las notas de versión.
4. Vuelve a poner la tarjeta, enciende y comprueba la versión como en el paso 8 de la opción A.

---

## 3 · Volver atrás

1. Consigue el ZIP de la versión anterior y **reinstálalo** con la opción A o B de arriba.
2. Si además quieres recuperar tu configuración tal como estaba: CONFIGURACIÓN → **RESPALDO**
   → arrastra el `backup.zip` que guardaste → **Restaurar y reiniciar firmware**.
   - La restauración **no** trae tu WiFi (el respaldo no la guarda, a propósito): si hiciera
     falta, se vuelve a configurar.
   - Restaurar **sustituye** la configuración actual por la del respaldo. Si calibraste algo
     después de hacerlo, eso se pierde.
3. Después de volver atrás, igual que tras instalar: mueve cada eje un poco con el JOG y la
   mano en la seta antes de hacer HOME.

---

## 4 · Si la interfaz no carga

Ve por orden y para en cuanto funcione:

1. **Recarga la página** una o dos veces. Tras una actualización, la primera carga puede
   venir de la memoria del navegador.
2. **Comprueba la dirección.** Prueba con la dirección IP de la máquina en vez del nombre, o
   con `http://192.168.1.1` si estás conectado al punto de acceso **RotechOS**.
3. **Borra los datos del sitio** en el navegador (en los ajustes del sitio: «Borrar datos» o
   «Olvidar este sitio») y vuelve a abrirla. Eso quita la versión guardada de la interfaz.
4. **Prueba desde otro dispositivo** (el móvil, otro ordenador). Si allí funciona, el problema
   es del primer navegador.
5. Si en ningún dispositivo carga: apaga la máquina, saca la tarjeta y comprueba que
   `www/index.html` existe en ella. Si falta o la actualización se quedó a medias, vuelve a
   copiar la carpeta `www/` del ZIP (opción B).
6. Si sigue sin cargar, pide ayuda y cuenta **qué versión instalaste, qué pasos seguiste y qué
   ves en pantalla**. Si la interfaz llega a abrir, CONFIGURACIÓN → IDENTIDAD → *Para soporte
   técnico* → **COPIAR SOPORTE** reúne los datos que te van a pedir.

La máquina **no necesita la interfaz para parar**: la seta roja, cableada en serie con la
alimentación como debe estar, la corta pase lo que pase en la pantalla.
