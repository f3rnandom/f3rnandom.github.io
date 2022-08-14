---
layout: post
title: "bspwm post"
author: Fernando Mendoza
---

## Configuracion de BSPWM en Debian GNU/Linux

Bspwm es un poderoso administrador de ventanas minimalista para Linux. Es altamente configurable y propone un enfoque innovador para la gestión de ventanas. Bspwm está escrito en C y se puede configurar en cualquier idioma. 

El presente archivo explica los pasos que podemos realizar para poder tener una configuracón de bspwm de manera correcta y exitosa.

## Ventajas

- Consumo mínimo de recursos del sistema.
- La configuración del gestor puede alterarse sobre la marcha, ejecutando comandos con bspc.
- Es muy estable y nunca lo he visto fallar.
- Muy versátil, en principio capaz de adaptarse a las manías de una amplia variedad de usuarios.

#### Aplicaciones Necesarias
Se puede instalar de esta forma con permisos de super usuario `root` o compilado como se muestra mas abajo.

- apt install bspwm	//gestor de ventanas
- apt install compton	//transparencias descontinuado
- apt install feh		//transparencias
- apt install rofi		//Ejecutar aplicaciones
- apt install polybar	//barra de estado   ----> Compilacion mas abajo
- apt install picom		//transparencias ---> compilacion mas abajo
- apt install slim slimlock	//bloquear pantalla

### Instalación de BSPWM 
---
`Dependencias`

Arch Linux: 
> $ sudo pacman -S libxcb xcb-util xcb-util-wm xcb-util-keysyms

Ubuntu/Debian: 
>$ sudo apt-get install libxcb-xinerama0-dev libxcb-icccm4-dev libxcb-randr0-dev libxcb-util0-dev libxcb-ewmh-dev libxcb-keysyms1-dev libxcb-shape0-dev

Debian: 
>apt install build-essential git vim xcb libxcb-util0-dev libxcb-ewmh-dev libxcb-randr0-dev libxcb-icccm4-dev libxcb-keysyms1-dev libxcb-xinerama0-dev libasound2-dev libxcb-xtest0-dev libxcb-shape0-dev

### Compilación y Instalación de BSPWM y SXHKD
---
>   $ git clone https://github.com/baskerville/bspwm.git
>	$ git clone https://github.com/baskerville/sxhkd.git
>	$ cd bspwm && make && sudo make install
>	$ cd ../sxhkd && make && sudo make install

### Desinstalar BSPWM

>   $ cd bspwm && sudo make uninstall
>	$ cd ../sxhkd && sudo make uninstall

### Correr BSPWM
Copiar los archivos de ejemplo en el directorio `~/.config/bspwm/bspwmrc` Y `~/.config/sxhkd/sxhkdrc`

Crear las carpetas en `bspcm` y `sxhkd` en .config
$ mkdir -p ~/.config/{bspwm,sxhkd}

Copiar los archivos de configuracion en las carpetas creadas anteriormente.
$ cp /usr/local/share/doc/bspwm/examples/bspwmrc ~/.config/bspwm/
$ cp /usr/local/share/doc/bspwm/examples/sxhkdrc ~/.config/sxhkd/
$ chmod u+x ~/.config/bspwm/bspwmrc

Creamos el archivo .xinitrc o xprofile para que corra de al encender el equipo y agregar la line de abajo.
[nano /home/usuario/.xinitrc]

```bash
exec bspwm 
```

### Configurar Archivo de bspwmrc

[nano /home/usuario/.config/bspwm/bspwmrc]

Agregamos al inicio del archivo
---
```bash
sxhkd &
compton --config /home/usuario/.config/compton.conf &
#para no afecte al java para uso del burssip
wmname LG3D &
#fondo de pantalla
feh --bg-fill /home/usuario/imagenes/nombreFondo.extencion &
#lanzar polybar
~/.config/polybar/launch.sh &
```
Agregamos al final del archivo
```bash
#tecla prefix definida tecla windows
bspc config pointer_modifier mod1
#Regla Para caja abra en modo flotante
bspc rule -a Caja desktop=’^8’ state=floating follow=on
#Autofocus con el puntero del mouse
bspc config focus_follows_pointer true
#Arregla el puntero de la barra de polybar
xsetroot -cursor_name left_ptr &
```
---
### Configuración sxhkd [Modificaciones]
[nano /home/usuario/.config/sxhkd/sxhkdrc]

```bash
#program launcher
rofi -show run
# 	super + alt r o q 		[salir sesion]
#focus the node in the given direction	[Mover entre ventanas]
super + {_,shift + }{Left,Down,Up,Right}
bspc node -{f,s} {west,south,north,east}

#Preselect the direction			[defenir donde abrir nueva ventana]
super + ctrl + alt + {_,shift + }{Left,Down,Up,Right}
bspc node -{f,s} {west,south,north,east}

#Move a floating window		[mover ventanas modo flotante]
super + ctrl + {Left,Down,Up,Right}
bspc node ...

# cancel the preselection for the focused node
super + ctrl + space
bspc node -p cancel
 
# cancel the preselection for the focused desktop	//cancel previzualizado
super + ctrl + alt + space
bspc query -N -d | xargs -I id -n 1 bspc node id -p cancel

# expand a window by moving one of its side outward
#super + alt + {h,j,k,l}
#   bspc node -z {left -20 0,bottom 0 20,top 0 -20,right 20 0}
 
# contract a window by moving one of its side inward
#super + alt + shift + {h,j,k,l}
#   bspc node -z {right -20 0,top 0 20,bottom 0 -20,left 20 0}

#Custom move/resize
alt + ctrl + {Left,Down,Up,Right}
/home/usuario/.config/bspwm/scripts/bspwm_resize  {west,south,north,east}

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
Para configurar el shortcut de #Custom move/resize debemos crear un directorio
> [Crear directorio ~/.confi/bspwm/scripts]
Creamos el script para poder ejecutar las acciones que modifican el tamaño
> [touch bspwm_resize con permisos de ejecucion]
Codigo de archivo `bspwm_resize`
```bash
#!/usr/bin/env dash

if bspc query -N -n focused.floating > /dev/null; then
step=20
else
step=100
fi

case “$1” in
west) dir=right; falldir=left; x=”-$step”; y=0;;
east) dir=right; falldir=left; x=”$step”; y=0;;
north) dir=top; falldir=bottom; x=0; y=”-$step”;;
south) dir=top; falldir=bottom; x=0; y=”$step”;;
esac

bspc node -z “$dir” “$x” “$y” || bspc node -z “$falldir” “$x” “$y”
```

### Instalar Polybar

Intalación [Compilación]

Para la Compilación https://github.com/polybar/polybar/wiki/Compiling
Instalar dependencias:

> apt install build-essential git cmake cmake-data pkg-config python3-sphinx python3-packaging libuv1-dev libcairo2-dev libxcb1-dev libxcb-util0-dev libxcb-randr0-dev libxcb-composite0-dev python3-xcbgen xcb-proto libxcb-image0-dev libxcb-ewmh-dev libxcb-icccm4-dev
Instalar Dependencias_2
> apt install libxcb-xkb-dev libxcb-xrm-dev libxcb-cursor-dev libasound2-dev libpulse-dev i3-wm libjsoncpp-dev libmpdclient-dev libcurl4-openssl-dev libnl-genl-3-dev

Descargar Polybar
> git clone --recursive https://github.com/polybar/polybar
> cd polybar

Descomprimir
> tar -xf polybar-x.x.x.tar

Borrar el archivo comprimido
> rm polybar-3.6.3.tar

Acceder a la carpeta polybar
> cd polybar

Crear directorio build y accedemos a el
> mkdir build
> cd build
Comenzamos el proceso de compilación
> cmake ..
> make -j$(nproc)``# Optional. This will install the polybar executable in /usr/local/bin
> sudo make install

### Configurar Polybar

Clonar el repositorio en 'Descargas':
> git clone https://github.com/VaughnValle/blue-sky.git
Configurar con los siguientes comandos:
> cd ~/Descargas/blue-sky/polybar/
> cp * -r ~/.config/polybar
> echo '~/.config/polybar/./launch.sh' >> ~/.config/bspwm/bspwmrc 
> cd fonts
> sudo cp * /usr/share/fonts/truetype/
> fc-cache -v

### Picom 
Para realizar la compilación e instalación de picom realizamos los siguientes comandos:

> apt update
Intalar dependencias
> sudo apt install meson libxext-dev libxcb1-dev libxcb-damage0-dev libxcb-xfixes0-dev libxcb-shape0-dev libxcb-render-util0-dev libxcb-render0-dev libxcb-randr0-dev libxcb-composite0-dev libxcb-image0-dev libxcb-present-dev libxcb-xinerama0-dev libpixman-1-dev libdbus-1-dev libconfig-dev libgl1-mesa-dev libpcre2-dev libevdev-dev uthash-dev libev-dev libx11-xcb-dev libxcb-glx0-dev
Clonamos repositorio
> git clone https://github.com/ibhagwan/picom.git
> cd picom/
Procedemos a la compilación
> git submodule update --init --recursive
> meson --buildtype=release . build
> ninja -C build
> sudo ninja -C build install


### Configurar el Picom
Creamos directorio `picom` en `.config` 
> mkdir ~/.config/picom
Accedemos al directorio
> cd ~/.config/picom
Copiamos el archivo `blue-sky/picom.conf`
> cp ~/Descargas/blue-sky/picom.conf .
Codificamos los efectos de picom
> nano picom.conf

Codigo de picom
> nano ~/Descargas/blue-sky/picom.conf
```bash
#Aplicar los bordeados:
echo 'picom --experimental-backends &' >> ~/.config/bspwm/bspwmrc 
echo 'bspc config border_width 0' >> ~/.config/bspwm/bspwmrc
#[border_width 0	//define grosor del bordeado]
```
Aplicar los bordeados:
> echo 'picom --experimental-backends &' >> ~/.config/bspwm/bspwmrc 
> echo 'bspc config border_width 0' >> ~/.config/bspwm/bspwmrc
> #[border_width 0	//define grosor del bordeado]


### Instalación de Slim SlimLock
Este es una utilidad que nos ayuda a bloquear la seción.

Para la instalación, primero instalamos las dependencias necesarias:
> sudo apt install slim libpam0g-dev libxrandr-dev libfreetype6-dev libimlib2-dev libxft-dev
> sudo apt update
>   ---> Elegir gestor de sesión slim

Intalamos slimlock
```bash
cd ~/Descargas/
git clone https://github.com/joelburget/slimlock.git
cd slimlock/
sudo make
sudo make install
cd ~/Descargas/blue-sky/slim
sudo cp slim.conf /etc/
sudo cp slimlock.conf /etc
sudo cp -r default /usr/share/slim/themes
```
### Configuracion slimlock
```bash
cd /usr/share/slim/themes/default
#Eliminar el imagen panel.png
#Copiar una imagen deseada con el nomnre panel.png
#establecer permisos 664 a la imagen
chmod 664
```
# Configuración de rofi
Crear directorio
> mkdir -p ~/.config/rofi/themes
Copiar archivo de configuración de repo antes descargado
> cp ~/Descargas/blue-sky/nord.rasi ~/.config/rofi/themes
Para seleccionar el tema
> rofi-theme-selector
Seleccionamos el tema
> Nord

### Incorporar Fuente 
Descargarla la fuente del sitio [Nerdfonts](https://www.nerdfonts.com/:
elegir la hard nerd fonts
`https://github.com/ryanoasis/nerd-fonts/releases/download/v2.1.0/Hack.zip`
Moverlo al directorio fonts
> mv /home/usuario_name/Descargas/Hack.zip /usr/local/share/fonts
Descomprimirlo
> unzip hack.zip
Eliminar el archivo comprimido
> rm Hack.zip

### Instalar Power Level 10k

Instalacion manual en el repositorio oficila de [powerlevel10k](https://github.com/romkatv/powerlevel10k#manual)
> -->	{Debemos realizarlo como usuario normal y como root}
Clonamos el repositorio en el directorio `Descargas`
> git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ~/powerlevel10k
> echo 'source ~/powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc
Dar inicio a la Configuracion con el comando
> zsh
- Elegir las preferencias deseadas

#### Retocar Power Level 10k
Abrir el archivo de configuracion de `powerlevel10k`
> nano .p10k.zsh
Comentar la linea
> vcs	#git status
agregar enseguida
```bash
context
command_execution_time
status
#Comnetar la toda linea contenida de POWERLEVEL9K_RIGHT_PROMT_ELEMENTS
```
Asignar zsh a root y al usuario
> usermod --shell /usr/bin/zsh tuUsuario
> usermod --shell /usr/bin/zsh root

### Firefox
Descargar [firefox] página oficial (https://www.mozilla.org/es-MX/firefox/new/)
Copiarlo al directorio `opt`
> cp firefox.xxxx.tar /opt
Descomprimir 
> tar -xf firefox.xxx.tar
Eliminar comprimido 
> rm firefox.xxx.tar
Intalar la herramienta firejail, el cual nos ayudad aprotegernos de ataques inseguros. 
> apt install firejail -y

### Shortcuts
***********************************************
windows + s 			          //ventanas Flotante
windows  + f			          //Pantalla completa
windows + t			              //ventanas normal acoplar
windows + izquierda/derecha	      //selecci0n de ventana
windows + m			              //fullscreen
ctrl + windows + alt + izq/der/abajo/arriba 		//pervio para lanzar ventana
windows + alt + r		            //Reset bspwm
windows + esc   
windows + alt + q		          //Cerrar bspwm 
ctrl + windows			          //desplazar ventana de modo flotante
windows + shift +numero 1-9	      //desplazar ventana al numero de entorno 
***********************************************