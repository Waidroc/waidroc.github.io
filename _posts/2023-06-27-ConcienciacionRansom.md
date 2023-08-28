---
title: La importancia de la concienciación de los usuarios en la ciberseguridad empresarial. Simulacro de Ransomware
date: 2023-06-25 11:00:00
categories: [Concienciacion, Ransomware]
tags: [concienciacion, batch, ransomware, windows, usuarios, ataques, simulacro]    
author: "Waidroc"
---


![Portada](/assets/img/2023-06-27/portada2.jpg)


 
La ciberseguridad es un aspecto `fundamental` en cualquier empresa, pero muy a menudo subestimado, ya que no se valora en la mayoría de los casos el verdadero impacto que tiene sobre una organización. Mientras que las medidas técnicas de protección son vitales, no debemos pasar por alto el `factor humano`. Los usuarios son el `eslabón más débil` en la cadena de seguridad, y su concienciación y capacitación son cruciales para `proteger los activos` digitales de una organización. En este artículo, exploraremos la importancia de la concienciación de los usuarios en las empresas y enseñaremos un enfoque práctico para fomentar una buena cultura en ciberseguridad.

Los `ciberdelincuentes` están constantemente buscando nuevas formas de infiltrarse en las redes empresariales y robar información confidencial. A menudo, utilizan técnicas de ingeniería social, como el phishing, el malware y los ataques de ransomware, para explotar la falta de conocimiento y concienciación de los usuarios. Estas amenazas pueden provocar `daños significativos`, como pérdida de datos, tiempo de inactividad del sistema y daño a la reputación de la empresa.

Sin una cultura de concienciación y capacitación en ciberseguridad, los usuarios pueden ser fácilmente engañados para que hagan clic en enlaces maliciosos, descarguen archivos infectados o compartan información confidencial. Por lo tanto, `educar` a los empleados sobre las mejores prácticas de seguridad y promover una mentalidad proactiva hacia la ciberseguridad se vuelve `imprescindible`.

![Estadistica](/assets/img/2023-06-27/estadistica.jpeg)

La teoría por sí sola no es suficiente para garantizar la seguridad. Es crucial que las empresas brinden a sus empleados `herramientas y recursos prácticos` para comprender y enfrentar las amenazas en línea. Estos podrían ser algunos de los recursos para dar un enfoque práctico y así fomentar la concienciación en ciberseguridad en tu empresa u organización:

✓ **Programas de capacitación y sensibilización:** desarrolla programas de capacitación interactivos y periódicos que aborden temas clave de ciberseguridad, como el phishing, la gestión de contraseñas, el uso seguro de dispositivos móviles y la identificación de posibles amenazas. Utiliza ejemplos reales y casos de estudio para ilustrar los riesgos y las consecuencias de los ataques cibernéticos.

✓ **Simulaciones de ataques y ejercicios de phishing:** realiza simulaciones de ataques de phishing y ransomware para evaluar la respuesta de los empleados y su capacidad para identificar y reportar posibles amenazas. Proporciona retroalimentación constructiva y utiliza estos ejercicios como oportunidades de aprendizaje para mejorar la concienciación en ciberseguridad.

✓ **Repositorios y recursos de referencia:** crea un repositorio interno de recursos de ciberseguridad, como guías prácticas, infografías y consejos de seguridad. Estos recursos pueden ayudar a los empleados a recordar y aplicar las mejores prácticas de seguridad en su trabajo diario. Incluye enlaces a recursos externos confiables para que los empleados puedan estar actualizados sobre las últimas amenazas y técnicas de protección.

✓ **Políticas y prácticas de seguridad claras:** establece políticas claras de seguridad de la información y asegúrate de que todos los empleados las conozcan y las cumplan. Promueve prácticas como el bloqueo de pantallas, el uso de contraseñas fuertes y el cifrado de datos para garantizar una mayor protección de los activos digitales de la empresa.


## Simulación de Ransomware

Un ejemplo claro para la concienciación, es realizar una simulación con un fichero que contenga un `falso Ransomware`, dirigido a los usuarios (no hay mejor aprendizaje que el hacer que "caigan en la trampa").


![Script](/assets/img/2023-06-27/batch.PNG)
<center> Código fuente del script </center>
<br>
El simulacro puede basarse en el envío de un `e-mail`, similar a uno legítimo, pero que contenga el fichero, comprimido en formato `zip`, para evadir algunos antivirus que puedan bloquear su descarga. También podríamos dejar el fichero en el escritorio o en un lugar recurrente del usuario, fuera del horario laboral.

Una manera óptima de administrar y ver los resultados, sería crear una `tabla con varios campos` (aviso al recibir e-mail, aviso tras descargar y ver fichero, fichero abierto, sin aviso...) e ir anotando como se va comportando cada uno de las víctimas. Al final del simulacro, podría hacerse un `gráfico` que presente de manera muy visual el comportamiento que tiene nuestra plantilla en este momento sobre el ataque realizado.

La ejecución del script se observaría de la siguiente forma:

![Ejecucion](/assets/img/2023-06-27/ejecucion.gif)




> Descarga de [`fakeRansom`](https://github.com/Waidroc/fakeRansom)
{: .prompt-tip}



## Conclusión

La ciberseguridad es un `desafío constante` en el mundo empresarial, y la concienciación de los usuarios desempeña un papel fundamental en la protección de los activos digitales de una empresa. Mediante la implementación de programas de capacitación y sensibilización, simulaciones de ataques y el acceso a recursos prácticos, las empresas pueden `fortalecer` la postura de seguridad y reducir los riesgos asociados con las amenazas cibernéticas. Recordemos que la seguridad es `responsabilidad de todos`, y al promover una cultura de ciberseguridad, estaremos construyendo una base sólida para enfrentar los desafíos digitales en el mundo empresarial actual.
