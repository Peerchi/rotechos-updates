# RotechOS

Interfaz web y macros para fresadoras CNC con **RepRapFirmware 3.5.4** sobre la placa
**BigTreeTech Scylla V1.0**. Todo vive en la tarjeta SD de la placa y se abre desde el
navegador del móvil, la tableta o el ordenador: no hay que instalar nada en el PC.

> Este repositorio es el **canal de descargas** de RotechOS. Aquí están las versiones
> publicadas y el aviso de actualización que consulta la interfaz (`manifest.json`).

## Descargar

**[Última versión →](https://github.com/Peerchi/rotechos-updates/releases/latest)**

En la página de la versión, en *Assets*:

| Fichero | Para qué |
|---|---|
| `rotechos-vAAAA.MM.zip` | La tarjeta SD completa: interfaz, macros, configuración base, firmware del módulo WiFi, la plantilla de calibración y las guías. |
| `firmware.bin` | RepRapFirmware 3.5.4 para la Scylla. **Solo** si tu placa es nueva o lleva otra versión. Si una versión no lo trae, mira el paso 1 de abajo. |
| `SHA256SUMS.txt` | Huellas para comprobar que la descarga está entera. |

Cada versión trae la SD completa: si vas varias versiones atrás, basta con instalar la última.

## Antes de nada: lo que esto NO es

- **No está validado en tu máquina.** Se desarrolla sobre una fresadora concreta y cada
  versión indica en sus notas qué falta por probar con la máquina encendida. La primera vez,
  **sin material, sin fresa dentro de la pieza y con la mano en la seta de emergencia**.
- **No tiene marcado CE.** Es un kit / retrofit: quien monta la máquina final es el
  integrador, con su propia autodeclaración.
- **No reanuda un trabajo tras un corte de luz.** Apaga el husillo y sube la Z, pero el
  trabajo hay que relanzarlo.
- **No trae la calibración de nadie.** Pasos por mm, corrientes, área de trabajo y sonda los
  pones tú: copiar los de otra máquina es la forma más rápida de estrellar un eje.

## Qué necesitas

- Placa **BTT Scylla V1.0** (STM32H723, drivers TMC2160).
- Fresadora de 3 ejes X/Y/Z (el 4.º eje A es opcional y viene apagado), finales de carrera
  y una sonda Z de contacto.
- Una tarjeta microSD y un ordenador para copiar ficheros.
- Una seta de emergencia cableada en la alimentación. La interfaz no la sustituye.

## Instalación en una placa nueva, en resumen

La guía completa, con la actualización y la vuelta atrás, está en
**[GUIA_INSTALACION.md](GUIA_INSTALACION.md)** (también va dentro del ZIP).

1. **Firmware de la placa.** Si la Scylla no lleva RepRapFirmware 3.5.4, copia `firmware.bin`
   en la raíz de la SD, ponla en la placa y enciéndela: el bootloader lo graba y lo renombra a
   `FIRMWARE.CUR`. Si la versión no trae `firmware.bin`, el de la Scylla sale del port de
   RepRapFirmware para placas STM32 de
   [TeamGloomy](https://github.com/gloomyandy/RepRapFirmware/releases)
   (placas compatibles: [teamgloomy.github.io](https://teamgloomy.github.io/supported_boards.html)).
2. **La SD.** Descomprime el ZIP y copia el **contenido** de su carpeta `ROTECHOS/` a la raíz
   de la tarjeta: tienen que quedar `www/`, `sys/`, `macros/`, `firmware/` y `licenses/`.
3. **El módulo WiFi**, solo si la placa es nueva y no aparece ninguna red: con la placa
   conectada por USB y un terminal serie a 115200 baudios, envía `M997 S1`. Se hace una vez.
4. **Primer arranque.** Si no encuentra tu WiFi, a los ~35 s crea su propia red
   **RotechOS** (clave `rotechos`). Conéctate y abre `http://192.168.1.1`. Desde
   CONFIGURACIÓN → Red pones tu WiFi.
5. **Tu calibración.** Sin `config-machine.g` la máquina arranca con valores prudentes de
   reserva (poca velocidad, área pequeña). El asistente de primer arranque te lleva a medir y
   guardar los tuyos. Si prefieres partir de un fichero, en el ZIP está
   `rotechos-sys/config-machine.g.ejemplo`: léelo entero, cámbialo y cópialo como
   `0:/sys/config-machine.g`.
6. **Antes del primer HOME**, mueve cada eje un poco con el JOG y comprueba que va hacia donde
   dice la pantalla. Un eje al revés se corrige en CONFIGURACIÓN → MOTORES.

## Actualizar

La interfaz avisa sola cuando hay una versión nueva. La instalación no es automática:
haz una copia (CONFIGURACIÓN → RESPALDO), descarga el ZIP y sigue la sección 2 de la guía.
Tu calibración (`config-machine.g`) y tu WiFi no viajan en el ZIP y no se pisan.

## Licencia y código fuente

RotechOS se distribuye bajo **GPL-3.0** (ver `ROTECHOS/licenses/` dentro del ZIP). Es una
obra derivada de Duet Web Control (Duet3D Ltd). La interfaz y las macros van en el ZIP como
código fuente legible, sin compilar ni ofuscar.

`firmware.bin` es RepRapFirmware 3.5.4 sin modificar (GPL-3.0, Duet3D Ltd) en su port para
placas STM32. Su código fuente está en
[Duet3D/RepRapFirmware](https://github.com/Duet3D/RepRapFirmware) y
[gloomyandy/RepRapFirmware](https://github.com/gloomyandy/RepRapFirmware).
RepRapFirmware y Duet son marcas de Duet3D Ltd; este proyecto no está afiliado a Duet3D.

## Si algo va mal

Abre una incidencia contando qué placa y qué versión de RotechOS tienes, qué esperabas y qué
pasó. Si la interfaz abre, CONFIGURACIÓN → IDENTIDAD → **COPIAR SOPORTE** reúne los datos que
hacen falta.
