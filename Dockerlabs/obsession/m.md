# Obsession – DockerLab

## Identificación del objetivo

**IP objetivo:** `172.17.0.2`

## Reconocimiento y análisis

Se utilizó Nmap para identificar los puertos y servicios accesibles de la máquina objetivo.

![Resultado del reconocimiento inicial](images/image1.PNG)

En este análisis se identificaron tres puertos abiertos: FTP (21), SSH (22) y HTTP (80).

## Análisis de los servicios encontrados

- **FTP (puerto 21):** servicio utilizado para la transferencia de archivos. Se revisó si permitía acceso anónimo o si exponía archivos y directorios.
- **SSH (puerto 22):** servicio utilizado para la administración remota del sistema. Podía convertirse en un punto de acceso si se obtenían credenciales válidas.
- **HTTP (puerto 80):** servicio web accesible desde el navegador. Se examinó su contenido, sus directorios y posibles recursos expuestos.

## Enumeración específica

Luego de identificar los servicios abiertos, se revisaron con mayor detalle para encontrar información útil. En primer lugar, se ingresó al servicio FTP mediante `ftp 172.17.0.2`, utilizando el acceso anónimo para listar y descargar los archivos disponibles. Después, se revisó la página web con `curl http://172.17.0.2` y se buscaron directorios accesibles mediante Gobuster, encontrando rutas como `/backup` e `/important`. Finalmente, se consideró el servicio SSH como una posible vía de acceso remoto, siempre utilizando las credenciales obtenidas durante el análisis del laboratorio.

![Evidencia de la enumeración](images/image2.PNG)

![Directorios o recursos encontrados](images/image3.PNG)

## Obtención del acceso inicial

Durante la revisión de los archivos y la página web se logró identificar el usuario `russoski`. Sin embargo, la contraseña no estaba expuesta directamente, por lo que se realizó una prueba controlada con Hydra utilizando un diccionario de contraseñas. Al encontrar una credencial válida, `iloveme`, se ingresó a la máquina mediante SSH con el comando `ssh russoski@172.17.0.2`. De esta manera, se obtuvo acceso al sistema como un usuario común, sin permisos administrativos.

## Enumeración interna

Una vez dentro de la máquina mediante SSH, se revisó el entorno del usuario para conocer la cuenta utilizada y sus permisos. Para ello, se comprobaron la identidad del usuario y los privilegios disponibles mediante comandos como `whoami`, `id` y `sudo -l`. Durante esta revisión se observó que el usuario `russoski` podía ejecutar la herramienta **Vim** con permisos de administrador y sin ingresar una contraseña. Este hallazgo fue registrado como una configuración insegura y como un posible medio para realizar una escalada de privilegios.

![Evidencia de la enumeración interna](images/image4.PNG)

## Identificación del vector de escalada

Al revisar los permisos del usuario `russoski`, se encontró que podía ejecutar **Vim** como administrador sin necesidad de ingresar una contraseña. Esta configuración no es segura porque Vim no solo permite editar archivos, sino que también puede realizar otras acciones del sistema. Por ese motivo, un usuario con permisos limitados podría aprovechar esta autorización para realizar acciones con privilegios de administrador.

![Evidencia del vector de escalada](images/image5.PNG)

## Resultado del laboratorio

Se logró obtener acceso a la máquina vulnerable utilizando el usuario `russoski` y se identificó una configuración insegura en los permisos de `sudo`, que permitía ejecutar **Vim** con privilegios de administrador. Con este hallazgo se confirmó la existencia de un posible vector de escalada de privilegios y se demostró que la máquina podía quedar completamente comprometida debido a una configuración incorrecta de permisos.
