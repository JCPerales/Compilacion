# Compilación

## 1. Instalación de compiladores GNU CC de C, C++ y Fortran desde el repositorio BASE
GNU Compiller Collector (GCC) es un conjunto de compiladores de GNU, entre los lenguajes que soporta se encuentran C, C++, Objective-C, Fortran, Java, Ada y Go.

Para compilar fuentes de C, C++ y Fortran, podemos instalar desde el repositorio *base* gcc, gcc-c++, gcc-gfortran. Esto lo podemos hacer con yum de la siguiente manera: (es necesario tener privilegios de administrador)

~~~~
$ yum install gcc gcc-c++ gcc-gfortran
~~~~

## 2. Instalación de programas desde fuentes
Con los compiladores instalados desde el repositorio base, instalar tmux, htop, pidstat y smartctl en el directorio $HOME. Para ello **NO** hay que iniciar sesion como root.
La convención en el cluster es instalar los programas de la siguiente forma:
`programas/<nombreprograma>/<version>_<compilador>-<version del compilador>_<librerias>-<version libs>`

Esta misma convención se usará para la instalación de los programas mencionados anteriormente.

NOTA: Es importante leer el archivo **README** incluido en la carpeta descargada de cada programa y el archivo **INSTALL** para seguir el proceso de instalación.
NOTA2: No siempre se incluyen los archivos README y/o INSTALL, aunque es muy raro.

Para descargar los paquetes se utilizó el comando wget y para desenpaquetar el comando tar

### 2.1 tmux

Tmux depende de dos librerias para poder funcionar, una de ellas es libevent y la otra ncurses (Ver README de tmux). Por esta razón, instalamos primero libevent y ncurses

#### 2.1.1 libevent 

Creamos directorios hasta obtener una jerarquia de la forma:
`$HOME/libs/libevent/2.0.21`

Descargamos libevent, en este caso se utilizó la versión 2.0.21, descargada desde:
http://libevent.org/

Descomprimimos el archivo y nos movemos a la carpeta donde lo descomprimimos

Ejecutamos

~~~~
    $ ./configure --prefix=$HOME/libs/libevent/2.0.21
	$ make
	$ make install
~~~~

Verificamos el directorio `$HOME/libs/libevent/2.0.21/` deben existir los siguientes directorios:
- bin
- include
- lib

#### 2.1.2 ncurses

Creamos directorios hasta obtener una jerarquia de la forma:
`$HOME/libs/ncurses/6.0`

Descargamos ncurses, en este caso se utilizó la versión 6.0, descargada desde:
http://ftp.gnu.org/gnu/ncurses/

Descomprimimos el archivo y nos movemos a la carpeta donde lo descomprimimos

Ejecutamos

~~~~
    $ ./configure --prefix=$HOME/libs/ncurses/6.0
	$ make
	$ make install
~~~~

Verificamos el directorio `$HOME/libs/ncurses/6.0/` deben existir los siguientes directorios:
- bin
- include
- lib
- share

#### 2.1.3 tmux

Una vez que tenemos instalados libevent y ncurses, procedemos a instalar tmux
La versión utilizada en tmux es la 2.5 descargada desde: 
https://github.com/tmux/tmux/wiki

Creamos los directorios, de modo que obtenemos algo similar a lo siguiente:

`$HOME/apps/tmux/2.5/`

Descomprimimos tmux y nos movemos al directorio creado.

Utilizaremos variables de entorno para definir en dónde tenemos las librerias libevent y ncurses (para no tener rutas tan largas), definimos las siguientes variables de entorno

~~~~
	$ LIBEVENT=$HOME/libs/libevent/2.0.21
	$ NCURSES=$HOME/libs/ncurses/6.0
~~~~

Exportaremos cuatro variables de entorno, la información sobre éstas variable fue obtenida de `./configure --help`

~~~~
	$ export LIBEVENT_CFLAGS=-I$LIBEVENT/include
    $ export LIBEVENT_LIBS=-L$LIBEVENT/lib
	$ export LIBNCURSES_CFLAGS=-I$NCURSES/include
	$ export LIBNCURSES_LIBS=-L$NCURSES/lib
~~~~

Con esto le decimos a `configure` que busque las librerias en esas rutas, ahora ejecutamos
~~~~
    $ ./configure --prefix=$HOME/apps/tmux/2.5
~~~~

Si todo sale bien, ya deberia haber configurado correctamente y procedemos con
~~~~
    $ make
	$ make install
~~~~

Verificamos el directorio $HOME/apps/tmux/2.5/ deben existir los directorios:
- bin
- share

### 2.2 htop

htop necesita de la librería ncurses, como ya la instalamos en el punto 2.1.2, simplemente la usaremos para hacer la instalacion de htop.
La versión utilizada es la 2.0.2, obtenida desde:

http://hisham.hm/htop/releases/

Similar a lo que hicimos con tmux, creamos la siguiente jerarquia de directorios:

`$HOME/apps/htop/2.0.2/`

Descomprimimos htop y en dentro del directorio creado ejecutamos
~~~~
    $ ./configure LDFLAGS=-L/$HOME/libs/ncurses/6.0/lib CPPFLAGS=-I/$HOME/libs/ncurses/6.0/include --prefix=/$HOME/apps/htop/2.0.2
	$ make
	$ make install
~~~~

Verificamos el directorio `$HOME/apps/htop/2.0.2/` deben existir las carpetas:
- bin
- share

### 2.3 pidstat (sysstat)
http://sebastien.godard.pagesperso-orange.fr/download.html

### 2.1 smartctl (S.M.A.R.T. Monitoring Tools)
smartctl es una utileria contenida en smartmontools, instalamos entonces, smartmontools.
La versión utilizada es la 6.5 obtenida de: 

https://sourceforge.net/projects/smartmontools/files/

Creamos la jerarquia de directorios:

`$HOME/apps/smartmontools/6.5/`

Descomprimimos smartmontools, nos movemos a la carpeta creada y ejecutamos

~~~~
	$ ./configure --prefix=$HOME/apps/smartmontools/6.5
	$ make
	$ make install
~~~~

Verificamos el directorio $HOME/apps/smartmontools/6.5/ deben existir las carpetas:
- etc
- sbin
- share

## Compiladores GNU CC 6.4, 7.1

https://gcc.gnu.org/
