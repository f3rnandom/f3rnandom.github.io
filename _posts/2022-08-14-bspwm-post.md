---
layout: post
title: "bspwm post"
author: Fernando Mendoza
---
# Instalación  y configuración básica de BSPWM


## BSPWM
***
Bspwm es un poderoso administrador de ventanas minimalista para Linux. Es altamente configurable y propone un enfoque innovador para la gestión de ventanas. Bspwm está escrito en C y se puede configurar en cualquier idioma.

### Ventajas
---
- Consumo mínimo de recursos del sistema.

- La configuración del gestor puede alterarse sobre la marcha, ejecutando comandos con bspc.

- Es muy estable y nunca lo he visto fallar.

- Muy versátil, en principio capaz de adaptarse a las manías de una amplia variedad de usuarios.

### Aplicaciones Necesarias
---
Podemos realizar la instalación de las herramientas necesarias con el gestor de paquetes, en caso de las distribuciones derivadas de base `Debian` usaremos `apt`, `dnf` para derivadas de Fedora y `pacman` para distros basadas en Arch, por otro lado podemos llevar acabo las instalaciones de las mismas realizando la compilación para ello deberemos seguir la documentación oficial de cada herramienta...

- bspwm  //Gestor de ventanas
- sxhkd //Gestor de shortcuts aunque puede venir con bspwm integrado
- polybar //barra de estado 
- feh //Establecer Fondos de pantalla
- picom //Transparencias 
- rofi //Ejecutar aplicaciones
- tilix //Emulador de terminal
-  slim 
- slimlock //Bloquear pantalla
- pcmanfm //Administrador de Archivos
- firejail //Herramienta que restringe el entorno de ejecución de aplicaciones no confiables 
  
## Instalación de BSPWM
---
Si deseamos realizar la compilación de `bspwm` seguiremos la guía en la wiki oficial de [bspwm](https://github.com/baskerville/bspwm/wiki).
Una vez que lo tengamos instalado, procedemos a su configuración.

### Iniciar BSPWM
---
Para iniciar el bspwm crearemos o modificaremos el archivo `~/.xinitrc` o `~/.xprofile` con la expresión de `exec bspwm` al final del archivo, bspwmrc lanza sxhkd por ti.

> nano /home/user_name/.xinitrc

### Configuración de BSPWM
---
Creamos dos carpetas en el directorio .config ubicado en ‘/home/user_name/.config’ (Si este no existe debemos crearlo).

Crear directorio para bspwm

  > mkdir bswm

Crear directorio para sxhkd
> mkdir sxhkd

Copiamos los archivos de ejemplo tanto para bspwm como para sxhkd a los directorios correspondientes creados anteriormente.
Para bspwm:
> cp /usr/share/doc/bspwm/examples/bspwmrc ~/.config/bspwm

Para sxhkd:
> cp /usr/share/doc/bspwm/examples/sxhkdrc ~/.config/sxhkd

Asignamos permisos de ejecución al archivo bspwmrc.
> chmod +x ~/.config/bspwm/bspwmrc

Abrimos el archivo bspwmrc para agregar la ejecución de las demás herramientas que nos permitirán un control y una visualización del sistema de manera eficiente y agradable a la vista.
> nano ~/.config/bspwm/bspwmrc

En caso de contar con dos monitores aplicar.

	   bspc monitor HDMI-0 -d I III V VII IX
	   bspc monitor eDP -d II IV VI VIII X

##### Donde HDMI-0 y eDP son el nombre que se le otorga a la salida de los monitores este pueden variar, verificarlo con el comando xrandr.

```bash
# Opcion para hacer autofocus con el puntero
bspc config focus-follows-pointer true

#Ejecutar herramientas
$HOME/.config/bspwm/autorun.sh
```
Cerramos el archivo. Y creamos un archivo de texto en .config/bspwm con el nombre de `autorun.sh`.
> nano .config/bspwm/autorun.sh

Otorgamos permisos de ejecución:
> chmod +x .config/bspwm/autorun.sh

Creamos nuestro script de bash para ejecutar las demás herramientas y características.
```bash
#!/bin/bash

# correccion de puntero de la X
xsetroot -cursor-name left_ptr &

#establecer fondo
feh --bg-fill /home/user_name/Images/fondo.jpg

#Iniciar polybar
$HOME/.config/polybar/launch.sh

#Iniciar picom
picom --experimental-backends &

#Definir monitor principal en caso d contar con dos.
#donde HDMI-0 puede variar el nombre de salida del monitor.
xrandr --output HDMI-0 --primary
```
 Otras opciones a considerar:
 ```bash
#evitar errores con java para uso del burssip
wmname LG3D &

#tecla prefix definida tecla windows
bspc config pointer_modifier mod1

#Regla Para caja abra en modo flotante
bspc rule -a Caja desktop=’^8’ state=floating follow=on

#Autofocus con el puntero del mouse
bspc config focus_follows_pointer true

#Arregla el puntero de la barra de polybar
xsetroot -cursor_name left_ptr &
```

## Configuración sxhkd - Modificaciones
---
Editamos el archivo sxhkdrc el cual se encarga de gestionar los shortcuts (combinación de teclas) para abrir las aplicaciones y manejar la tailing window.
> nano ~/.config/sxhkd/sxhkdrc

Revisar los cambios realizados:
```bash
#Terminal emulator
super + Retunr
	xfce4-terminal

#Program launcher
super + @Space
	rofi -show run

#focus the node in the given direction [Mover entre ventanas]
super + {_,shift + }{Left,Down,Up,Right}
	bspc node -{f,s} {west,south,north,east}

#Preselect the direction [defenir donde abrir nueva ventana]
super + ctrl + alt + {_,shift + }{Left,Down,Up,Right}
	bspc node -{f,s} {west,south,north,east}

#Move a floating window [mover ventanas modo flotante]
super + ctrl + {Left,Down,Up,Right}
	bspc node ...

# cancel the preselection for the focused node
super + ctrl + space
	bspc node -p cancel

# cancel the preselection for the focused desktop //cancel previzualizado
super + ctrl + alt + space
	bspc query -N -d | xargs -I id -n 1 bspc node id -p cancel

# expand a window by moving one of its side outward
#super + alt + {h,j,k,l}
# bspc node -z {left -20 0,bottom 0 20,top 0 -20,right 20 0}

# contract a window by moving one of its side inward
#super + alt + shift + {h,j,k,l}
# bspc node -z {right -20 0,top 0 20,bottom 0 -20,left 20 0}

#Custom move/resize
alt + ctrl + {Left,Down,Up,Right}

#Open firefox
super + shift + f
	firejail /opt/firefox/firefox

#Google Chrome
super + shift + g
	google-chrome

#Cerrar sesion
super + shift + x
	slimlock
```
### Configurar Polybar
---
Creamos una carpeta con el nombre de polybar en el directorio .config
> mkdir ~/.config/polybar

Una vez que tengamos instalado el polybar procedemos a copiar el archivo que nos viene con una configuración por default.
> cp /etc/polybar/config.ini ~/.config/polybar

Crear un archivo ejecutable (script) que contenga la ejecución lógica de la barra de ejemplo de polybar como lo indican en la [wiki](https://github.com/polybar/polybar/wiki) de esta. “$HOME/.config/polybar/launch.sh” :

```bash
#!/usr/bin/env bash

# Terminate already running bar instances# If all your bars have ipc enabled, you can use

#polybar-msg cmd quit

# Otherwise you can use the nuclear option:

killall -q polybar

# Launch bar1 and bar2

echo "---" | tee -a /tmp/polybar1.log /tmp/polybar2.log

polybar example  >>/tmp/polybar1.log 2>&1 &

#polybar bar2 2>&1 | tee -a /tmp/polybar2.log & disown

echo "Barra en Lanzamiento..."
```
Una vez creado le asignamos los permisos de ejecución necesarios.
> chmod +x ~/.config/polybar/launch.sh
  
### Configuración de Picom
---
Esta herramienta nos permite poner las transparencias para nuestro Tailing Window. Creamos su directorio respectivo en .config:
> mkdir ~/.config/picom

Clonamos un archivo de configuración del siguiente repositorio de github. en el directorio de `Descargas` este solo con fines prácticos y organización.
> cd Descargas

Clonamos el Repositorio:
> git clone [https://github.com/lorem10/lemo-dotfiles](https://github.com/lorem10/lemo-dotfiles)

Una vez concluido la clonación del repositorio, procedemos a copiar el archivo de configuración de `picom` a nuestro directorio en ~/.config/picom
> cp lemo-dotfiles/config/picom/picom.conf ~/.config/picom


### Cambiar tema de Rofi
---
En la terminal escribimos el siguiente comando:
> rofi-theme-selector

Elegimos el tema “android_notification by Rasi” o el que les agrade...

Oprimimos la combinacion de teclas `alt + a` para aplicar el tema elegido.
  

### SlimLock
---
Este es una utilidad que nos ayudara al bloquear el sistema.

### Configuracion slimlock
---
Este punto puede ser opcional, el objetivo de este es establecer un fondo diferente al que viene por defecto.

Accedemos a la ruta siguiente
> cd /usr/share/slim/themes/default

- Eliminar el imagen panel.png

- Copiar una imagen deseada con el nomnre panel.png a este mismo directorio (remplazando el existente anteriormente).

- Establecer los permisos 664 a la imagen.
> chmod 664 panel.png

# Configuración de rofi
---
Crear directorio de themes
> mkdir -p ~/.config/rofi/themes

Descargamos el tema en el directorio correspondiente.
> curl -o ~/.config/rofi/themes/nord.rasi https://raw.githubusercontent.com/amayer5125/nord-rofi/master/nord.rasi

Para seleccionar el tema, ejecutamos en la terminal:
> rofi-theme-selector

Seleccionamos el tema, y oprimir la combinación de teclas `alt + a` para establecer el tema elegido.
> Nord

### Incorporar Fuente
---
Descargarla la fuente del sitio oficial [Nerdfonts](https://www.nerdfonts.com/)

Descargar la Hack Nerd Fonts:

`https://github.com/ryanoasis/nerd-fonts/releases/download/v2.1.0/Hack.zip`

Moverlo al directorio fonts (Este puede variar).
> mv ~/Descargas/Hack.zip /usr/local/share/fonts

O pude ser:
> mv ~/Descargas/Hack.zip /usr/share/fonts/TTF/

Descomprimirlo
> unzip hack.zip

Eliminar el archivo comprimido
> rm Hack.zip
