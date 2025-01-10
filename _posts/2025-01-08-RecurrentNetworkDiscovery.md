---
title: Detección automática de hosts desconocidos en nuestra red
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

Para ello, desarrollaremos, implementaremos y configuraremos una herramienta, la cual nos facilitará la tarea, en nuestro entorno Kali Linux, basándose en la automatización del descubrimiento de hosts, realizando escaneos periódicos y recibiendo notificaciones vía Telegram, cuando un nuevo dispositivo se conecta a cualquiera de las redes que tengamos identificadas.

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

<h2>Configuración de redes a monitorizar</h2>

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


<h2>Script para el escaneo inicial</h2>

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

# Crear directorios si no existen
mkdir -p $KNOWN_HOSTS_DIR $LOG_DIR

# Escaneo inicial
while read -r RED; do
    echo "Iniciando escaneo inicial para la red: $RED"
    OUTPUT_FILE="$KNOWN_HOSTS_DIR/$(echo $RED | tr '/' '_').txt"

    # Escaneo rápido con Nmap en modo ping-only
    nmap -sn "$RED" -oG - | awk '/Up$/{print $2}' > "$OUTPUT_FILE"

    echo "Resultados iniciales guardados en $OUTPUT_FILE"
done < "$CONFIG_FILE"

echo "Escaneo inicial completado. Verifica los resultados en $KNOWN_HOSTS_DIR"
```

Otorgamos al script permisos de ejecución:

```bash
chmod +x /home/waidroc/Tools/periodicNetworkDiscovery/scripts/escaneo_inicial.sh
```

<h2>Configuración del bot de Telegram para las notificaciones</h2>

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


<h2>Script para escaneos periódicos con notificaciones</h2>

El siguiente script está basado en el escaneo de redes con Nmap, comparando los resultados con el archivo histórico. A su vez, notificará por Telegram lo snuevos hosts detectados, agragando los nuevos activos a la lista de dispositivos conocidos si aparecen al menos 3 veces en los respectivos escaneos que vaya realizando.

Creamos el fichero detectar_nuevos_hosts.sh:

```bash
nano /home/waidroc/Tools/periodicNetworkDiscovery/scripts/detectar_nuevos_hosts.sh
```

El contenido debe de ser el siguiente:

```bash
#!/bin/bash

# Directorios y configuración
CONFIG_FILE="/home/waidroc/Tools/periodicNetworkDiscovery/configs/redes_a_monitorizar.txt"
KNOWN_HOSTS_DIR="/home/waidroc/Tools/periodicNetworkDiscovery/output/known_hosts"
KNOWN_DEVICES_FILE="/home/waidroc/Tools/periodicNetworkDiscovery/output/dispositivos_conocidos.txt"
LOG_DIR="/home/waidroc/Tools/periodicNetworkDiscovery/output/logs"
BOT_TOKEN="xxxxxxxxxxxxxxxxxxxxxx"
CHAT_ID="xxxxxxxxxxxxx"

# Crear directorios y archivo de dispositivos conocidos si no existen
mkdir -p $KNOWN_HOSTS_DIR $LOG_DIR
touch $KNOWN_DEVICES_FILE

# Función para enviar notificaciones a Telegram
send_notification() {
    NUEVOS_HOSTS=$1
    # Reemplazar saltos de línea por espacios para evitar /n/n en Telegram
    FORMATTED_HOSTS=$(echo "$NUEVOS_HOSTS" | tr '\n' ' ')
    MESSAGE="🚨 *Nuevos hosts detectados:*\n\n$FORMATTED_HOSTS"
    
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

<h2> Automatizar el descubrimiento y monitorización en múltiples redes</h2>

Una vez guardado el script, automatizaremos la tarea con Cron, configurandolo para ejecutar este script periódicamente, por ejemplo cada 60 minutos:

```bash
crontab -e
```

Agregaremos la línea:

```bash
0 * * * * /bin/bash /home/waidroc/Tools/periodicNetworkDiscovery/scripts/detectar_nuevos_hosts.sh >> /home/waidroc/Tools/periodicNetworkDiscovery/output/logs/cron.log 2>&1
```





AÑADIR IMAGEN
![flag](/assets/img/2023-02-17/flag.png)




`Muchas gracías por leer, Waidroc :)`



