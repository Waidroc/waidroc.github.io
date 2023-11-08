---
title: Canarytokens - Una solución sencilla y eficaz para tu organización.
date: 2023-11-08 9:00:00
categories: [Blue Team, Detección, Monitorización]
tags: [canarytoken, defensa, concepto, monitorizacion, deteccion, recurso, herramienta, tutorial, honeypot]    
author: Waidroc
---

![TITULO!](/assets/img/2023-11-08/ct_titulo.png)


¡Hola a todxs!  👋🏻 

En el siguiente post, vamos a desarrollar el concepto de Canarytoken y, vamos a comprobar cómo este sencillo recurso puede "salvarnos" contra atacantes que no han sido detectados a la hora de realizar una intrusión en cualquier dispositivo de nuestra infraestructura.

En el sector de la ciberseguridad, la detección temprana de amenazas es esencial para proteger sistemas y datos críticos. Para lograr este objetivo, las organizaciones han recurrido durante mucho tiempo a los "Honeytokens" como trampas virtuales. Sin embargo, recientemente, ha surgido una solución aún más efectiva, versátil y eficaz: los Canarytokens.

# ¿Qué son los Canarytokens?

![CANARYTOKENS!](/assets/img/2023-11-08/Canarytokens-Logo-01.png)

Los Canarytokens han nacido para sustituir a los Honeytokens, los cuales son señuelos de seguridad, en forma de ficheros, diseñados para atraer a posibles atacantes y alertar a los administradores de sistemas sobre actividades sospechosas. Estos tokens se colocan en lugares estratégicos, como documentos, ficheros o sistemas, y están diseñados para parecer atractivos para los atacantes. Cuando un atacante accede o intenta acceder a un Honeytoken, se dispara una alerta, lo que permite a los administradores tomar medidas para defenderse.

Sabiendo esto sobre los Honeytokens, es cuando entra a la acción el concepto de Canarytoken, los cuales son una evolución de éstos ya que, en adición a lo comentando anteriormente, los Canarytokens son balizas digitales que generan una alerta cuando se activan. Pueden encontrarse en múltiples escenarios, podiendo llegar a  ser enlaces web, ficheros, direcciones de correo electrónico, etc. Cuando uno de éstos se activa, se notifica a los administradores, lo que indica un posible intento de ataque o acceso no autorizado. 

Para terminar de entender bien su funcionamiento, podemos aclararlo com una analogía: un águila (atacante), estando en un bosque (nuestra infrastructura), intenta conseguir una presa para calmar su apetito (información confidencial). Avista una presa fácil (fichero en URI susceptible de tener información confidencial *C:\Administracion*), el cuál se trata de un animal indefenso (fichero susceptible de tener información muy confidencial *credenciales.txt*). Cuando ésta realiza el ataque, el pobre animalito, desprende un sonido característico para así, avisar a sus demás compañeros para que huyan del ave depredadora, con el fin de escapar de sus garras (aviso a responsables de IT de la organización).

![ANALOGÍA!](/assets/img/2023-11-08/analogia.jpeg)


### ¿Por qué los Canary Tokens son una mejora sobre los honeytokens?

Los Canary Tokens ofrecen diversas ventajas clave sobre los honeytokens:

⧫ **Versatilidad:** Pueden ser implementados en diferentes contextos, como sistemas de archivos, correos electrónicos, documentos, bases de datos y más. Esto los hace adecuados para una amplia gama de aplicaciones.

⧫ **Discreción:** A menudo, los honeytokens son fácilmente reconocibles como señuelos, lo que puede desalentar a los atacantes. Los Canary Tokens, por otro lado, pueden camuflarse de manera efectiva como datos legítimos.

⧫ **Detección precisa:** Los Canary Tokens generan alertas inmediatas cuando se toca, proporcionando información detallada sobre el tipo de actividad sospechosa que se ha producido.

⧫ **Mejor adaptación a entornos de producción real:** Los honeytokens pueden interrumpir los procesos y sistemas de producción, mientras que los Canary Tokens se integran de manera más fluida en el entorno real sin causar interrupciones significativas.

### ¿Cómo se implementan los Canary Tokens?

Tenemos dos vías para implementar los Canarytokens en nuestra organización.

La más sencilla y ágil, y en la que nos centraremos en la prueba de concepto en este post, la cual se basa en crear los tokens desde su web oficial, la cual no requiere de ningún conocimiento adicional. Solo necesitaríamos estudiar dónde vamos a implementarlo y qué queremos obtener de él.

Como alternativa, tenemos la opción de crear nuestro propio servidor de Canarytokens, estableciendo una conexión directa con el servidor principal a la hora de crearlos y mantener las comunicaciones. Esto podría tener sentido si deseamos colocar algún tipo de canarytoken, en endpoints o servidores que no tienen conexión a Internet, dando lugar así a el establecimiento de la conexión vía HTTPS localmente y que, nuestro servidor de Canarytokens, se encargue de mantener las comunicaciones con el servidor externo principal, además de notificar vía e-mail de cuando la baliza es manipulada, para así notificar a los responsables de las balizas y que puedan tomar medidas de seguridad inmediatas.


## Prueba de Concepto












<h1>🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧     UNDER CONSTRUCTION     🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧🚧</h1>  
