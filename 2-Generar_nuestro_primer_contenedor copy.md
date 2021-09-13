## 2 - Generar nuestro primer contenedor
Ahora que tiene todo configurado en su ambiente, es hora de ensuciarnos las manos. En esta sección, ejecutará una [Alpine Linux](http://www.alpinelinux.org/) container (a lightweight linux distribution) 
en su sistema y luego ejecutara el comando `docker run`.

Para comenzar, ejecutemos lo siguiente en nuestra terminal:
```
$ docker pull alpine
```

> **Nota:** Dependiendo de cómo haya instalado Docker en su sistema, es posible que vea un `permission denied` error después de ejecutar el comando anterior. Pruebe los comandos del tutorial de introducción a [verify your installation](https://docs.docker.com/engine/getstarted/step_one/#/step-3-verify-your-installation). Si está en Linux, es posible que deba utilizar `sudo` delante de comando `docker`. Como alternativa puedes [create a docker group](https://docs.docker.com/engine/installation/linux/ubuntulinux/#/create-a-docker-group) para arreglar este inconveniente.

El commando `pull` busca la alpine **image** de **Docker registry** y la guarda en nuestro sistema. Puede utilizar el comando `docker images` para ver todas las imagenes alojadas en nuestro sistema.
```
$ docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
alpine                 latest              c51f86c28340        4 weeks ago         1.109 MB
hello-world             latest              690ed74de00f        5 months ago        960 B
```

### 2.1 Docker Run
Bien! Ahora vamos a correr **container** que utilicen como base esta imagen. Para hacer esto vas a utilizar el comando `docker run`.

```
$ docker run alpine ls -l
total 48
drwxr-xr-x    2 root     root          4096 Mar  2 16:20 bin
drwxr-xr-x    5 root     root           360 Mar 18 09:47 dev
drwxr-xr-x   13 root     root          4096 Mar 18 09:47 etc
drwxr-xr-x    2 root     root          4096 Mar  2 16:20 home
drwxr-xr-x    5 root     root          4096 Mar  2 16:20 lib
......
......
```
¿Qué paso? Por detras, ocurrieron un montón de cosas. Cuando se ejecuto `run`,
1. El Docker client contacto al Docker daemon.
2. El Docker daemon valido en nuestro disco (alpine en este caso) se encuentra disponible localmente, en caso de no estarlo, la descargara desde la página de Docker. (Como anteriormente ejecutamos `docker pull alpine`, el paso de la descarga no es necesario)
3. El Docker daemon genera el contenedor y luego ejecuta el comando sobre dicho contenedor.
4. El Docker daemon transmite la salida del comando al Docker client.

Cuando ejecutamos `docker run alpine`, pasando los parametros (`ls -l`), se mostro el contenido interno del contenedor.

Hagamos algo más interesante.

```
$ docker run alpine echo "hello from alpine"
hello from alpine
```
Bien, obtenemos el echo, en este caso el Docker client ejecuto el `echo` en nuestro contenedor de alpine y luego hizo exit. Si notaste, esto se ejecuto de una manera bastante rápida, imaginate si para hacer esto tuviera que encender un equipo, ejecutar el echo y luego hacer exit, con esto ya podes evidenciar que tan rápido son los contenedores...

Probemos este otro comando.
```
$ docker run alpine /bin/sh
```

Okay..., no recibimos nada! Ejecutamos el comando mal? Bueno, no. Estos shells interactivos se cerrarán después de ejecutar cualquier comando con script, a menos que se ejecuten en una terminal interactiva; por lo tanto, para que este ejemplo no salga, debe de ejecutarse `docker run -it alpine /bin/sh`.

Ahora te encontras parado adentro del contenedor `ls -l`, `uname -a` y muchos otros. Para salir del contenedor ejecutamos el comando `exit`.


Bien, es tiempo de utilizar el comando `docker ps`. El comando `docker ps` nos muestra los contenedores que estan corriendo en el sistema.

```
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

So no hay contenedores corriendo se vera una linea en blanco. Para poder ver más detalles al respecto podemos ejecuta `docker ps -a`

```
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
36171a5da744        alpine              "/bin/sh"                5 minutes ago       Exited (0) 2 minutes ago                        fervent_newton
a6a9d46d0b2f        alpine             "echo 'hello from alp"    6 minutes ago       Exited (0) 6 minutes ago                        lonely_kilby
ff0a5c3750b9        alpine             "ls -l"                   8 minutes ago       Exited (0) 8 minutes ago                        elated_ramanujan
c317d0a9e3d2        hello-world         "/hello"                 34 seconds ago      Exited (0) 12 minutes ago                       stupefied_mcclintock
```

Lo que se detalla en la lista anterior son las ejecuciones realizadas por los contenedores, la columna `STATUS` muestra hace cuanto fueron realizadas. Con lo realizado anteriormente, te estaras preguntando si es posible ejecutar más de un comando sobre un contenedor, corramos el siguiente comando:

```
$ docker run -it alpine /bin/sh
/ # ls
bin      dev      etc      home     lib      linuxrc  media    mnt      proc     root     run      sbin     sys      tmp      usr      var
/ # uname -a
Linux 97916e8cb5dc 4.4.27-moby #1 SMP Wed Oct 26 14:01:48 UTC 2016 x86_64 Linux
```
Ejecutando `run` con el parametro `-it` nos genera un tty interactivo en el contenedor. Ahora puede ejecutar tantos comandos en el contenedor como desee. Tómate un tiempo para ejecutar tus comandos favoritos.

Con esta guía dimos un vistazo a alto nivel sobre el comando `docker run` que de seguro sea el que utilices con más frecuenta, por ello dedicamos un poco de tiempo a familizarnos con el mismso. Para obtener más detalles sobre `run`, ejecuta el comando `docker run --help` para ver todas las opciones disponibles. A medida que vayamos avanzando, veremos más variantes del comando `docker run`.
### 2.2 Terminología
En los puntos anteriores, se manejaron muchos términos super específicos sobre Docker, y al ser la primera vez que nos enfrentamos a ellos, pueden generarnos alguna confusión, antes de seguir, vamos a aclarar algunos de los términos usados con mayor frecuencia en Docker.

- *Containers* - Running instances of Docker images &mdash; containers run the actual applications. A container includes an application and all of its dependencies. It shares the kernel with other containers, and runs as an isolated process in user space on the host OS. You created a container using `docker run` which you did using the alpine image that you downloaded. A list of running containers can be seen using the `docker ps` command.
- *Images* - El sistema de archivos y la configuración de nuestra aplicación que se utilizan para crear contenedores. Para obtener más información sobre una imagen de Docker ejecuta el comando `docker inspect alpine`. En el ejemplo anterior usamos el comando `docker pull` para descargar la imagen de **alpine**. Cuando ejecutas el comando `docker run hello-world`, realiza `docker pull` por detras para descargar la imagen de **hello-world**.
- *Docker daemon* - El servicio en segundo plano que se ejecuta en el host que gestiona la creación, ejecución y distribución de contenedores Docker.
- *Docker client* - The command line tool that allows the user to interact with the Docker daemon.
- *Docker Store* - Un [storage](https://store.docker.com/) de imágenes de Docker, donde puede encontrar contenedores, complementos y ediciones de Docker confiables y listos para la empresa. Lo usará más adelante en este tutorial.

## Proximos pasos
Para el siguiente paso del laboratorio, diríjase a [2.0 Webapps with Docker](https://github.com/ElLargo/DevOps-180736/blob/develop/3-Webapp_en_docker.md)
