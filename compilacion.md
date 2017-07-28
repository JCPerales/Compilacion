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

Verificamos el directorio `$HOME/apps/smartmontools/6.5/` deben existir las carpetas:
- etc
- sbin
- share

## 3. Compiladores GNU CC 6.4, 7.1

Instalaremos dos versiones de GCC, la 6.4 y la 7.1, ésto se hará para después instalar la librera OpenBLAS con diferentes versiones de GCC, la que instalamos con *yum* (Verificamos la versión con `$ gcc -v`, la salida en este caso es la version 4.4.7), y las versiones 6.4 y 7.1. Después de compilar OpenBLAS con cada versión de GCC, probaremos cuál versión compilada nos da un mejor rendimiento, para ello ocuparemos dgemm que hace uso de OpenBLAS. Posteriormente compilaremos ATLAS con cada versión de GCC y haremos la misma prueba realizada con OpenBLAS.

La idea es ver, cómo la compilación influye en el desempeño de un programa.

Ambas versiones de GCC fueron descargadas desde los mirrors que lista la página del proyecto GCC: https://gcc.gnu.org/

### 3.1 GNU CC 6.4

GNU CC depende principalmente de tres librerias, GMP, MPFR y MPC, por ello, antes de compilar GCC, instalamos las librerias pertinentes.

#### 3.1.1 GMP

Descargamos GMP, en este caso se utilizó la version 6.1.2, obtenida de:
https://gmplib.org/

Creamos la siguiente jerarquía de directorios `$HOME/libs/gmp/6.1.2/`

Descomprimimos GMP y dentro de la carpeta creada al descomprimir GMP, ejecutamos: 

~~~~
$ ./configure --prefix=$HOME/libs/gmp/6.1.2
$ make
$ make install
~~~~

Verificamos el directorio `$HOME/libs/gmp/6.1.2/` deben existir las carpetas:

- include
- lib
- share

#### 3.1.2 MPFR

Descargamos MPFR, la versión utilizada fue la 3.1.5, obtenida de: 
http://www.mpfr.org/

Creamos la jerarquía de directorios `$HOME/libs/mpfr/3.1.5`

Descomprimimos MPFR y nos movemos a la carpeta creada al descomprimir

Nota. MPFR depende de GMP, hay que tomar esto en cuenta a la hora de instalar MPFR, ya que tenemos que incluir la ruta donde se encuentra GMP

Una vez dentro de la carpeta que se creo al descomprimir, ejecutamos:

~~~~
$ ./configure --prefix=$HOME/libs/mpfr/3.1.5 --with-gmp=$HOME/libs/gmp/6.1.2
$ make
$ make install
~~~~

Al terminar, deberiamos tener creadas las siguientes carpetas en `$HOME/libs/mpfr/3.1.5/`

- include
- lib
- share

#### 3.1.3 MPC

Descargamos MPC, la version utilizada fue la 1.0.3, obtenida de : 
http://www.multiprecision.org/index.php?prog=mpc

Creamos la jerarquía de directorios `$HOME/libs/mpc/1.0.3/`

Nota. MPC depende de las librerias GMP y MPFR, que ya previamente se instalaron, al igual que con MPFR habrá que indicar a `configure` dónde se encuentran instaladas ésas librerias, dado que no las instalamos en la ruta default.

Descomprimimos MPC y nos movemos a la carpeta que se creó al descomprimir, una vez en ese directorio, ejecutamos :

~~~~
$ ./configure --prefix=$HOME/libs/mpc/1.0.3 --with-gmp=$HOME/libs/gmp/6.1.2 --with-mpfr=$HOME/libs/mpfr/3.1.5
$ make
$ make install
~~~~

Al terminar, deberiamos tener creadas las siguientes carpetas en `$HOME/libs/mpc/1.0.3/`

- include
- lib
- share

#### 3.1.4 GCC 6.4.0

Ya que tenemos instaladas las librerias mencionadas anteriormente, procedemos a compilar GCC.

Creamos la jerarquía de directorios siguiente `$HOME/comps/GCC/6.4.0/`, a diferencia de los programas y las librerias anteriores, los compiladores los instalamos bajo el directorio comps (compiladores), y usamos el mismo formato ya utilizado, el nombre del compilador, y la versión.

En la documentación de GCC se recomienda construir GCC en otro directorio diferente al directorio donde están los fuentes, por ello creamos otro directorio al mismo nivel del directorio que contiene los fuentes de GCC. En este caso se creó un directorio llamado `obj`, nos movemos al directorio creado y desde ahí ejecutaremos los siguientes comandos.


~~~~
$ export LD_LIBRARY_PATH=$HOME/libs/gmp/6.1.2/lib/:$HOME/libs/mpfr/3.1.5/lib/:$HOME/libs/mpc/1.0.3/lib/
~~~~

En el comando anterior, "exportamos" la variable de entorno `LD_LIBRARY_PATH` en la cual agregamos las rutas donde se encuentran las librerias GMP, MPFR y MPC, esto se debe a que GCC mandará a llamar otros scripts/programas para configurar, si no indicamos dónde encontrar las librerias, estos configuradores serán incapaces de encontrar dichas librerias

Ejecutamos ahora `configure` indicando dónde están las librerias, los lenguajes a habilitar (C, C++ y Fortran) y deshabilitamos "multilib".

~~~~
$ ../gcc-6.4.0/configure --enable-languages=c,c++,fortran --with-gmp=$HOME/libs/gmp/6.1.2 --with-gmp-include=$HOME/libs/gmp/6.1.2/include --with-gmp-lib=$HOME/libs/gmp/6.1.2/lib --with-mpfr=$HOME/libs/mpfr/3.1.5 --with-mpfr-include=$HOME/libs/mpfr/3.1.5/include --with-mpfr-lib=$HOME/libs/mpfr/3.1.5/lib --with-mpc=$HOME/libs/mpc/1.0.3 --with-mpc-include=$HOME/libs/mpc/1.0.3/include --with-mpc-lib=$HOME/libs/mpc/1.0.3/lib --prefix=$HOME/comps/GCC/6.4.0 --disable-multilib

$ make -j 4
$ make install
~~~~

Finalmente deberíamos tener las siguientes carpetas dentro de `$HOME/comps/GCC/6.4.0/` :

- bin
- include
- lib
- lib64
- libexec
- share

### 3.2 GNU CC 7.1

Dentro de la carpeta creada al extraer GCC-7 creamos tres carpetas, mpc, gmp y mpfr, dentro los respectivos fuentes de MPC, GMP y MPFR

Creamos la jerarquía de directorios `$HOME/comps/GCC/7.1.0`

Creamos una carpeta fuera de la carpeta de GCC-7, al mismo nivel de GCC-7 con el nombre `objdir`, nos vemos a dicha carpeta y desde dicha carpeta ejecutamos: 

~~~~
$ ../gcc-7.1.0/configure --enable-languages=c,c++,fortran --disable-multilib --prefix=$HOME/comps/GCC/7.1.0 --with-local-prefix=$HOME/comps/GCC/7.1.0
$ make
$ make install
~~~~
