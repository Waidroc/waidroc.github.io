---
title: Canarytokens - Una solución sencilla y eficaz para tu organización.
date: 2023-11-08 9:00:00
categories: [Blue Team, Detección, Monitorización]
tags: [canarytoken, defensa, concepto, monitorizacion, deteccion, recurso, herramienta, tutorial, honeypot]    
author: Waidroc
---

![TITULO!](/assets/img/2023-11-08/ct_titulo.png)


¡Hola a todxs!  👋🏻 

En el siguiente post, vamos a desarrollar el concepto de `Canarytoken` y, vamos a comprobar cómo este sencillo recurso, puede "salvarnos" contra atacantes que no han sido detectados a la hora de realizar una intrusión en cualquier activo de nuestra infraestructura.

En el sector de la ciberseguridad, la `detección temprana` de amenazas es esencial para proteger sistemas y datos críticos. Para lograr este objetivo, las organizaciones han recurrido durante mucho tiempo a los `Honeytokens` como trampas o balizas virtuales. Sin embargo, recientemente, ha surgido una solución aún más efectiva, versátil y eficaz: los Canarytokens.

## ¿Qué son los Canarytokens? #

![CANARYTOKENS!](/assets/img/2023-11-08/Canarytokens-Logo-01.png)

Los Canarytokens han nacido para `sustituir` a los Honeytokens, los cuales son señuelos de seguridad, en forma de ficheros, diseñados para atraer a posibles atacantes y alertar a los administradores de sistemas sobre actividades sospechosas. Estos tokens se colocan en lugares estratégicos, como documentos, ficheros o sistemas, y están diseñados para parecer atractivos con el fin de captar la atención de los atacantes. Cuando éstos acceden o intentan acceder a un Honeytoken, se dispara una alerta, lo que permite a los administradores tomar medidas para defenderse.

![HONEYTOKENS!](/assets/img/2023-11-08/honeytoken.png)


Sabiendo esto sobre los Honeytokens, es cuando entra a la acción el concepto de `Canarytoken`, los cuales son una `evolución` de éstos ya que, en adición a lo comentando anteriormente, los Canarytokens son balizas digitales que generan una alerta inminente al ser activados y además pueden encontrarse en múltiples escenarios, entre ellos,  pueden camuflarse entre enlaces web, ficheros, direcciones de correo electrónico, bases de datos y un largo etcétera. Cuando uno de éstos se activa, se notifica a los administradores, lo que indica un posible intento de ataque o acceso no autorizado. 

Para terminar de entender bien su funcionamiento, podemos aclararlo com una `analogía`: un `águila` (atacante), estando en un `bosque` (nuestra infrastructura), intenta conseguir una `presa` para calmar su apetito (información confidencial). Avista una presa `fácil` (fichero en URI susceptible de tener información confidencial *C:\Administracion*), el cuál se trata de un animal `indefenso` (fichero susceptible de tener información muy confidencial *credenciales.txt*). Cuando ésta realiza el ataque, el pobre animalito, desprende un `sonido` característico para así, `avisar` a sus demás compañeros para que huyan del ave depredadora, con el fin de `escapar` de sus garras (aviso a responsables de IT de la organización).

![ANALOGÍA!](/assets/img/2023-11-08/analogia.jpeg)


### ¿Por qué los Canarytokens son una mejora sobre los honeytokens?

Los Canarytokens ofrecen diversas `ventajas` clave sobre su predecesor:

⧫ **Versatilidad:** Pueden ser implementados en `diferentes contextos`, como sistemas de archivos, correos electrónicos, documentos, bases de datos y más. Esto los hace adecuados para una amplia gama de aplicaciones.

⧫ **Discreción:** A menudo, los honeytokens son fácilmente reconocibles como señuelos, lo que puede desalentar a los atacantes. Los Canarytokens, por otro lado, pueden `camuflarse de manera efectiva` como datos legítimos.

⧫ **Detección precisa:** Los Canarytokens generan `alertas inmediatas` cuando se activan, proporcionando información detallada sobre el tipo de actividad sospechosa que se ha producido.

⧫ **Mejor adaptación a entornos de producción real:** Los honeytokens pueden interrumpir los procesos y sistemas de producción, mientras que los Canarytokens se integran de manera más fluida en el entorno real `sin causar interrupciones` significativas.

### ¿Cómo se implementan los Canary Tokens?

Tenemos dos vías potenciales para implementar los Canarytokens en nuestra organización.

La más sencilla y ágil, y en la que nos centraremos en la prueba de concepto en este post, se basa en `crear los tokens desde su web oficial`, la cual no requiere de ningún conocimiento adicional. Solo necesitaríamos estudiar dónde vamos a implementarlo y qué queremos obtener de él, así como el tipo de cebo o señuelo donde introduciremos la baliza.

Como alternativa, tenemos la opción de crear nuestro propio `servidor de Canarytokens`, estableciendo una conexión directa con el servidor principal a la hora de crearlos y mantener las comunicaciones. Esto podría tener sentido si deseamos colocar algún tipo de canarytoken, en endpoints o servidores que no tienen conexión a Internet, dando lugar así al establecimiento de la conexión vía HTTP/HTTPS localmente y que, nuestro servidor de Canarytokens, se encargue de mantener las comunicaciones con el servidor externo principal, además de notificar vía e-mail de cuando la baliza es manipulada, para así, notificar a los responsables de las balizas y que puedan tomar medidas de seguridad inmediatas. Si tienes curiosidad en probar como funciona este método, te dejo el [`Github oficial`](https://github.com/thinkst/canarytokens) con los pasos para implementarlo.


## Prueba de Concepto

Lo primero que debemos de tener claro es, con qué finalidad vamos a generar un token, así como saber la ubicación final, tipo de canarytoken a generar de entre todas las opciones que la herramienta nos brinda y qué mensaje informativo vamos a indicarle si alguna vez un atacante llega a "clicar" en la baliza.

Después, nos iremos al [`sitio oficial`](https://canarytokens.org/generate) e iniciamos el proceso de generaración del token, siguiendo siempre los pasos indicados a continuación:

⧫ **Elección del tipo de token:** Determinaremos el tipo de token que se adapte mejor a nuestro entorno. Algunas opciones comunes incluyen documentos falsos, direcciones de correo electrónico falsas, diversos ficheros en sistemas (PDFs, Excels, Words...) y más.

⧫ **Generación de tokens:** Una vez identificado el tipo de Canarytoken que deseamos generar, debemos indicar un correo para la notificación en caso de activar la baliza, así como su descripción que iría en el e-mail enviado (deberá de ser lo más descriptiva posible, para así saber localizar el token vulnerado, en caso de que tengamos múltiples y diversos tokens distribuidos por toda la organización)

⧫ **Distribución estratégica:** Una vez generados, debemos de volver a estudiar y valorar estratégicamente donde colocarlos, valorando los activos de toda la superficie de nuestra organización (servidores, APIs, registros, correos electrónicos...).

⧫ **Monitorización y alertas:** Cuando un token se active, recibiremos una alerta que nos permitirá investigar la actividad sospechosa. Aquí es cuando entra en juego la importancia de la descripción añadida anteriormente, para así saber localizar "al toque", que baliza ha sido activada, identificando así el activo y la ubicación vulnerada.

⧫ **Respuesta y análisis:** Una vez activado un Canarytoken, debemos investigar el incidente para determinar la naturaleza de la amenaza y tomar medidas adecuadas para mitigarla.

En el segundo, vamos a ocultarlo dentro de un .exe ejecutable, intentando que un atacante crea que es un programa que oculta información importante (robo de programa en producción o desarrollo).

### Escenario 1

En el siguiente escenario, se ocultará el Canarytoken dentro de un fichero excel, en el que supuestamente, se deberían de estar guardando credenciales de diversas plataformas del usuario (banco, redes sociales...)



### Escenario 2

En este caso, el token se alojará dentro de un programa ejecutable (.exe). Se simulará el robo de un programa en desarrollo donde el atacante, querrá exfiltrar el programa mencionado para así, robárselo a la víctima y apoderarse de él y de sus futuras ganancias económicas y reputacionales.










<h1>🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧     UNDER CONSTRUCTION     🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧</h1>  
