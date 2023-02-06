---
title: WriteUp - NightCityCTF
date: 2023-02-07 9:00:00
categories: [Writeup]
tags: [writeup, ctf, reconocimiento, stego, OSINT]    
author: "Waidroc"
---

<h2> Introducción </h2>

Hola a todos! Antes de comenzar con la resolución de la máquina virtual, me gustaría comentar que dicha máquina pertenece a un taller de ciberseguridad realizado en Junio de 2022 en el instituto IES Pedro Mercedes, teniendo como asistentes cualquier interesado sobre la materia, entre ellos miembros de los cuerpos de Policía Local y Guardia Civil (Equipo @) de Cuenca.

La máquina fue elaborada por mi y mi compañero David Valero (@cillo31) y nos centramos en realizar un servidor muy vulnerable con una fácil resolución, basándose principalmente en el reconocimiento, esteganografía y tocando muy por encima el OSINT.

Consta de una máquina de nivel básico, estupenda para comenzar en el mundillo de los CTF, la cual desde mi punto de vista veo genial para pasar un buen rato, además de aprender una serie de técnicas y conceptos que serán muy valiosos para el futuro.

Todos los recursos son totalmente públicos y descargables, incluyendo en el mismo repositorio los enlaces de descarga de la máquina virtual (.ova) y a su vez la presentación en PowerPoint con su resolución explicada de una forma más amena, para poder ser presentada en público en cualquier momento (recurso perfecto para docentes).

Dicho esto, vamos a las consideraciones previas que tenemos que realizar para proceder a la resolución del CTF.

<h2> Consideraciones previas </h2>

Para poder realizar este CTF, debemos de descargar la imagen de la MV (bit.ly/3RtWAV7) e importarla en VirtualBox.

Una vez importada, debemos de meterla en la misma Red NAT que nuestra máquina atacante, en nuestro caso, utilizaremos Kali Linux, para que ambas se puedan comunicar entre sí.

![RedNat](/assets/img/2023-02-17/1.JPG)
![RedNat](/assets/img/2023-02-17/2.JPG)

<h2> Resolución </h2>

Lo primero de todo, es hacer un escaneo a la red para descubrir que IP tiene asignada nuestra máquina víctima.

![descubrirIP](/assets/img/2023-02-17/reconocimiento.png)

![linux](/assets/img/2023-02-17/linux.png)

![directorioTrabajo](/assets/img/2023-02-17/directorioTrabajo.png)

![nmapVuln](/assets/img/2023-02-17/nmapVuln.png)

![directorioContenido](/assets/img/2023-02-17/carpetacontenido.png)

![reminder](/assets/img/2023-02-17/descargarremindertxt.png)

![reminder2](/assets/img/2023-02-17/reminder.png)

![hosts](/assets/img/2023-02-17/etchosts.png)

![blog](/assets/img/2023-02-17/blog.png)

![commentsweb](/assets/img/2023-02-17/commentsweb.png)

![wfuzz](/assets/img/2023-02-17/wfuzz.png)

![nmapdirectorios](/assets/img/2023-02-17/nmapdirectorios.png)

![robots](/assets/img/2023-02-17/robotstxt.png)

![drobots](/assets/img/2023-02-17/descargarobotstxt.png)

![robin](/assets/img/2023-02-17/robin.png)

![robinw](/assets/img/2023-02-17/robinweb.png)

![dimagenes](/assets/img/2023-02-17/descargaimagenes.png)

![exiftool](/assets/img/2023-02-17/exiftool.png)

![map](/assets/img/2023-02-17/osint.png)

![cewlrobin](/assets/img/2023-02-17/diccionariorobin.png)

![japon](/assets/img/2023-02-17/japon.png)

![passBatman](/assets/img/2023-02-17/contraseñabatman.png)

![dCredenciales](/assets/img/2023-02-17/directoriocredencialespng.PNG)

![fCredenciales](/assets/img/2023-02-17/archivocredenciales.png)

![acceso](/assets/img/2023-02-17/acceso.png)

![ncImagen](/assets/img/2023-02-17/pasarimagendeservidor.png)

![gitclone](/assets/img/2023-02-17/gitclonestegsolve.png)

![stegsolve](/assets/img/2023-02-17/stegsolve.png)

![stegsolve2](/assets/img/2023-02-17/stegsolve2.png)

![aperisolve](/assets/img/2023-02-17/aperisolve.png)

![aperisolve2](/assets/img/2023-02-17/aperisolve2.png)

![passwd](/assets/img/2023-02-17/passwd.png)

![homejoker](/assets/img/2023-02-17/homejoker.PNG)

![flag](/assets/img/2023-02-17/flag.png)

![history](/assets/img/2023-02-17/alternativasHistory.png)

holaholaholaholaaaa
