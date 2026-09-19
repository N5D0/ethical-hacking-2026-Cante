# Alumna: Nahomy Dafne Cante Jiménez
# Viernes 18 de septiembre de 2026
# NMAP Recon Challenge

El presente archivo se refiere a un informe de reconocimiento técnico sobre ***scanme.nmap.org*** elaborado en un entorno seguro y con fines educativos-explorativos como parte de las actividades del *Hacker Women Council en su Segunda Generación*. Consta de diez apartados que corresponden a los 10 comandos elegidos de la **Guía Práctica NMAP con los 100 comandos básicos e intermedios**, consiguiendo identificar puertos, sistemas operativos, protocolos de transporte y el contraste entre dichas técnicas, por mencionar algunos ejemplos.

Entre las 13 imágenes incluidas se podrá observar los resultados obtenidos de la puesta en práctica de cada uno de los comandos empleados en una máquina virtualizada de *Linux (Kali)*, así como el análisis derivado del objetivo esperado y de los productos clave arrojados.
### 1. Comando para comprobar si el host está disponible

#### Resultado obtenido:
**(Imagen 1)**
![[1.png]]

**(Imagen 2)**
![[1.1.png]]

| Comando utilizado                             | Objetivo                                                                                                                                              | Resultados clave                                                                                                                                                                                                                                                                                                           |
| --------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ***nmap -sn scanme.nmap.org*** (imagen 1).    | Revisar si el host está activo al momento del análisis. Al usar el **-sn** se desactiva el escaneo de puertos y solo comprueba si el equipo responde. | - **Estado del host:** El host **scanme.nmap.org** se encuentra activo y respondiendo (_Host is up_).<br>    <br>- **Métrica de conectividad:** Se registró una latencia estable de **0.054 segundos (54 ms)** con un tiempo de respuesta total de **0.33 segundos**, lo que confirma una ruta de red directa y operativa. |
| ***nmap -sn -v scanme.nmap.org*** (imagen 2). | Agregar una bandera de verbosidad para determinar qué paquetes o protocolos se usaron para obtener el escaneo.                                        | Utilizó *ping*: el mecanismo predeterminado de descubrimiento de host.                                                                                                                                                                                                                                                     |
Iniciar con este comando fue interesante porque por medio de él se puede confirmar que el host está presente en la red, lo que posibilita que los siguientes comandos a ejecutar para su escaneo puedan completarse correctamente. En su caso contrario, más allá de imposibilitar el escaneo, también sería una señal de que no se pueden identificar posibles vulnerabilidades sin necesitar llevar a cabo una auditoría. En la _Imagen 1_ probé primero con mi IPv4 pública para conocer sus alcances.

Como un paso agregado, realicé el mismo comando agregándole **-v** para conocer más sobre su proceso, como se muestra en la _Imagen 2_, lo que arrojó que utilizó ***ping*** como mecanismo rápido y ligero, luego de realizar una resolución de nombres *DNS* para traducir el dominio a su dirección IP asociada. En otras palabras, en lo particular realicé una solicitud ***ICMP Echo*** y obtuve a cambio una ***ICMP Echo Reply***.  

### 2. Comando para escaneo TCP y reconocimiento de puertos abiertos

#### Resultado obtenido:
**(Imagen 3)**
![[2.png]]

**(Imagen 4)**
![[2.1.png]]

**(Imagen 5)**
![[2.2.png]]

| Comando utilizado                                             | Objetivo                                                                                                                                | Resultados clave                                                                                                                                                                                                    |
| ------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1) ***nmap -sT scanme.nmap.org*** (imagen 3).                 | Escaneo estándar del protocolo de transporte *TCP* para identificar los puertos abiertos.                                               | Encontró los servicios esenciales (22 y 80), pero al escanear libremente descubrió puertos de prueba altos específicos del servidor (9929 y 31337) y marcó el puerto 25 como bloqueado (_filtered_).                |
| 2) ***nmap -sT -p 1-1000 scanme.nmap.org*** (imagen 4).       | Este comando delimita la búsqueda a los primeros 1,000 puertos más comunes utilizando nuevamente el protocolo TCP estándar.             | Al limitar el rango con **-p 1-1000**, se detectaron únicamente el 22, 25 y 80, omitiendo los puertos altos antes identificados (como el 9929 y 31337), debido a que se encuentran fuera de esa numeración inicial. |
| 3) ***nmap -sS --top-ports 100 scanme.nmap.org*** (imagen 5). | Este comando precisa los servicios abiertos asociados a los puertos con mayor probabilidad estadística de estar activos a nivel global. | Al usar una estrategia de red distinta, identificó los habituales (22 y 80) y además descubrió el puerto **443 (HTTPS)**, demostrando que cada técnica detecta matices diferentes.                                  |
La combinación de estas tres técnicas (**-sT, -sS y -p 1-1000**) aporta un valor fundamental a la fase de reconocimiento, demostrando que **ningún método por sí solo ofrece una radiografía perfecta de la conexión de red**. Mientras que el escaneo por rango limitado (**-p 1-1000**) es ideal para acotar la búsqueda y ahorrar recursos en servicios comunes; las técnicas completas y de bajo nivel revelan detalles ocultos en puertos altos o variaciones en la respuesta del objetivo. En conjunto, este contraste de comandos evita falsos negativos y permite entender con precisión cómo reacciona el servidor ante distintos estímulos de red.

### 3. Comando para determinar los servicios que están ejecutándose en los puertos descubiertos

#### Resultado obtenido:
**(Imagen 6)**
![[3.1.png]]

| Comando utilizado                  | Objetivo                                                                                                                                                                                                                     | Resultados clave                                                                                                                                                                                                                        |
| ---------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ***nmap -sS -sV scanme.nmap.org*** | Escaneo *SYN* para detectar los puertos mediante un enfoque _half-open_. Además del complemento para descubrir el software exacto (por ejemplo, el tipo de servidor web o la versión de *SSH*) que corre detrás de cada uno. | Ambas técnicas (la conexión completa y el escaneo semiabierto o _half-open_) detectaron de forma idéntica la misma radiografía de puertos, es decir, tanto si se completa el protocolo de tres vías como si se corta a mitad de camino. |

Al combinar el escaneo *SYN* con la identificación de versiones (**-sV**), los resultados de puertos abiertos se mantuvieron completamente consistentes con las fases previas (**22, 80, 9929 y 31337**). Esta coincidencia demuestra la **fiabilidad del reconocimiento**: el agregado de sondas a nivel de aplicación no generó falsos positivos ni alteraciones en el comportamiento del objetivo ***scanme.nmap.org.***
### 4. Comando para escanear servicios sin conexión
#### Resultado obtenido:
**(Imagen 7)**
![[4.1.png]]
![[4.2.png]]

| Comando utilizado                             | Objetivo                                                                                     | Resultados clave                                                                                                                                                    |
| --------------------------------------------- | -------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ***nmap -sU --top-ports 20 scanme.nmap.org*** | Reconocimiento de puertos asociados con el protocolo *UDP* para tener una visión perimetral. | Se identificó únicamente el **puerto 123 (NTP - Network Time Protocol)** en estado abierto, revelando que el servidor expone un servicio de sincronización horaria. |

En razón de que el escaneo de los servicios *UDP* suele ser lento de acuerdo con la teoría, se acotó a una búsqueda de los 20 puertos más reconocidos y el resultado obtenido puede que no sea preciso, aún así, arrojó un listado con mayor número de puertos en estado mixto que cerrados. Se dice que los servicios *UDP* rara vez responden con un paquete de confirmación cuando están cerrados, por lo que podría plantearse que al ejecutar este comando es importante contrastarlo con una validación adicional cuando se quiere hacer una revisión de falsos positivos y, con ello, conocer los posibles servicios expuestos. 
### 5. Comando para utilizando scripts NSE
#### Resultado obtenido:
**(Imagen 8)**
![[5.png]]

| Comando utilizado                                          | Objetivo                                                                                                                                                            | Resultados clave                                                                                                                                 |
| ---------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| ***nmap -p 80 -sV --version-intensity 9 scanme.nmap.org*** | Análisis profundo de un servicio específico (Ej. *HTTP*<br>- puerto 80); aumentando la intensidad al 9 de sus sondas disponibles (donde la predeterminada es la 7). | - Confirmación del puerto **80/TCP abierto**.<br>    <br>- Identificación del servidor web **Apache** junto con su **número de versión exacto**. |

Este comando demuestra que, aunque un escaneo rápido nos dice que hay una web activa, aumentar la intensidad de la prueba garantiza extraer con total certeza el software exacto que está corriendo, un paso clave para detectar posibles vulnerabilidades asociadas a esa versión específica. Como parte del ejercicio de prueba se eligió probar el aumento de intensidad, pero en la práctica he aprendido que no siempre es lo recomendable cuando hace más lento el escaneo e innecesariamente envía varias pruebas adicionales secuenciales para su confirmación. 
### 6. Comando para detectar el sistema operativo 
#### Resultado obtenido:
**(Imagen 9)**
![[6.png]]

| Comando utilizado             | Objetivo                                      | Resultados clave                                                                                                                                                                                                                    |
| ----------------------------- | --------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ***nmap -O scanme.nmap.org*** | Comando para detección del Sistema Operativo. | - Verificación del estado de puertos base necesaria para el análisis de la pila de red.<br>    <br>- Generación de un listado de probabilidades de sistemas operativos (_OS guesses_) mediante técnicas de _TCP/IP fingerprinting_. |
Al ejecutar el comando para detectar el sistema operativo (**-0**), *Nmap* cumplió dos funciones: volvió a arrojar los puertos abiertos y generó una lista de probables sistemas operativos con un porcentaje de coincidencia para cada uno. Por lo que, aunque podría resultar medianamente confiable por los porcentajes obtenidos, el propósito de adivinar de forma remota qué plataforma está en uso en un dispositivo y sin necesidad de tener acceso al equipo es útil y de gran relevancia. Se asume que podría limitarse a crear ese listado cuando se trata de una máquina virtualizada. 

Esta información ayuda a construir el perfil técnico del objetivo a auditar y así saber si se está localizando a una máquina *Linux*, como es el caso, *Windows* o hasta un *router*; como las rutas de acceso y las vulnerabilidades cambian según cada uno de ellos, este comando podría ser de los primeros a emplear para iniciar un reconocimiento completo. 
### 7. Comando para realizar un escaneo con técnica distinta

#### Resultado obtenido:
**(Imagen 10)**
![[7.png]]

| Comando utilizado              | Objetivo                                                                         | Resultados clave                                                                                                                                                                                                                                                                                                                                                                                                                  |
| ------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ***nmap -sC scanme.nmap.org*** | Conocer los detalles específicos de la configuración criptográfica del servidor. | - **Consistencia de puertos:** Se reafirmó la presencia de los puertos abiertos habituales (22, 80, etc.).<br>    <br>- **Extracción de Llaves SSH (`ssh-hostkey`):** Se logró capturar las huellas digitales (_fingerprints_): las llaves públicas del servicio SSH en el puerto 22, incluyendo lo que podrían ser los hash criptográficos de cada una junto a la etiqueta del tipo de algoritmo: **RSA, DSA, ECDSA y ED25519.** |
Mientras que un escaneo de puertos tradicional solo se limita a confirmar qué puertos se localizan, si están o no abiertos, este profundiza al revelar algunos de los algoritmos criptográficos asociados al puerto 22. Esto es valioso cuando se requiere realizar una auditoría criptográfica hasta el punto de obtener una huella digital lo más fidedigna del servidor para prevenir ataques y hasta evaluar cuáles estándares mantiene activos que podrían descontinuarse para un correcto endurecimiento (*hardening*) del acceso remoto, como es el ejemplo del **DSA** considerado el más antiguo de ellos e inseguro por defecto. 

Al ser el puerto 22, el cual corresponde al **protocolo SSH**, el único que muestra los algoritmos y *hashes*, se reconoce un comportamiento normal al ser el único que exhibe habitualmente su identidad de forma abierta e inmediata; a pesar de que otro puerto localizado, como es el caso del 9929, también se vincule a comunicaciones cifradas.
### 8. Comando para comparar técnicas 

#### Resultado obtenido:
**(Imagen 11)**
![[8.png]]

| Comando utilizado                                   | Objetivo                                                                                      | Resultados clave                                                                                                                                                                  |
| --------------------------------------------------- | --------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ***nmap --script http-enum -p 80 scanme.nmap.org*** | Enumerar automáticamente las aplicaciones y los archivos web expuestos en el servidor *HTTP*. | - Confirmación del puerto **80/TCP abierto**.<br>    <br>- Detección de la ruta **/images/** con la función de listado de directorios habilitada (_directory listing_) de Apache. |
Como se ha señalado con anterioridad, en un escaneo rápido o básico de puertos lo único que se obtiene, en contraste con el ejemplo de comando presentado, es la verificación general de la disponibilidad del servicio *HTTP* (puerto 80). Obteniendo, con el *script* de enumeración web (**http-enum**), una profundidad de resultados con la finalidad para buscar rutas y directorios ocultos asociados al mismo.

Esta diferencia es clave para una auditoría de seguridad porque, al usar esta técnica, se puede verificar las posibles fugas de información y evaluar el nivel de riesgo al que está expuesto el sitio web. Es preocupante que este directorio observado (*/images/*) pudiera ser atacado o descargado si inicialmente no estaba destinado a ser de acceso público.
### 9. Comando para escaneo agresivo (combina OS, versiones, *scripts* y *traceroute*)
#### Resultado obtenido:
**(Imagen 12)**
![[9.1.png]]
![[9.2.png]]

| Comando utilizado             | Objetivo                                                                                                          | Resultados clave                                                                                                                                                                                              |
| ----------------------------- | ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ***nmap -A scanme.nmap.org*** | Agrupa múltiples técnicas avanzadas en un único comando para consolidar los hallazgos de reconocimiento integral. | Ejecución del escaneo agresivo (**-A**), consolidando en un solo comando la revalidación de puertos, las llaves SSH, las conjeturas de sistema operativo y un mapeo de ruta de red de **17 saltos (_hops_)**. |
Este comando, considerado de escaneo agresivo, es uno de los más interesantes al albergar más resultados de tipo cualitativo y cuantitativo que corresponden a vectores de reconocimiento (servicios, criptografía, sistema operativo y topología) en una sola ejecución. Encuentro que su importancia radica en que elimina la necesidad de realizar pruebas fragmentadas y con eso entregar una visión sólida de lo podría ser la infraestructura del objetivo.

Lo que más me ha interesado es lo reveladores que son los resultados para precisar la identidad del servidor desde sus entornos físico y lógico. El complemento de los 17 saltos, a lo que hasta ahora se había encontrado mediante el listado de comandos elegido, permite aproximarse a la revisión de cuál es el camino que se atraviesa en una red pública para llegar al destino esperado, generando así una panorámica global cuando de comprender una superficie se trata.
### 10. Comando para escaneo de puertos UDP 
#### Resultado obtenido:
**(Imagen 13)**
![[10.1.png]]
![[10.2.png]]

| Comando utilizado                             | Objetivo                                                | Resultados clave                                                                                                                                                                      |
| --------------------------------------------- | ------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ***nmap -sU --top-ports 20 scanme.nmap.org*** | Explora los 20 puertos UDP más comunes de forma segura. | Escaneo de los **20 puertos UDP principales**, que arrojó **18 puertos cerrados**, el **puerto 68** en estado mixto (_open\|filtered_) y el **puerto 123** completamente **abierto**. |
En razón de que los escaneos *UDP* completos suelen ser extremadamente lentos y propensos a falsos positivos en la red, como se recordó cuando se usó el comando de servicios relacionado con este protocolo, se acotó la ejecución de este comando a solo localizar los 20 puertos *UDP* principales. Se parte del entendido de que la revisión de los puertos *UDP* tiene una alta complejidad analítica debido a su naturaleza sin estado de este protocolo de transporte; una muestra pudiera ser el tiempo empleado para cumplir con lo solicitado: mientras que hacer el escaneo de puertos *TCP* tomó 1.35 segundos, este **se demoró 16.1 segundos más (17.45 segundos)**.

De este modo, puede que el resultado del total de 20 puertos identificados no represente una anomalía por sí misma. Sin embargo, por más de que solo haya sido el puerto 123 (asociado a *NTP*) el único abierto y el puerto 68 (asociado a *DHCP*) en estado mixto (*open/filtered*), cuando el resto aparece cerrados, confirma a la vez un panorama ideal siguiendo el **principio del mínimo privilegio**: donde solo aquellos puertos que sean estrictamente necesarios para operar sean los que estén encendidos y accesibles. 

# Análisis General
Lo conseguido con esta actividad de evaluación ha tenido como objetivo ser integral al combinar técnicas de sondeos de red, enumeración web y análisis perimetral. Los principales puntos de interés se concentraron en verificar la disponibilidad de los servicios clave, abarcando puertos *TCP* tradicionales asociados a la administración y a la web de una máquina, hasta puertos de soporte *UDP*, lo que permitió trazar una base clara y de contraste sobre la accesibilidad del objetivo. 

Entre los hallazgos más relevantes se destacan la identificación de los algoritmos criptográficos del servicio *SSH* en el puerto 22 y, de forma crítica, el descubrimiento del directorio **/images/** con el listado de archivos habilitado (*directory listing*) en el servidor web Apache. Esto último, sobre todo, porque podría dar cuenta de una debilidad de configuración significativa, ya que, como se comentaba con anterioridad, podría exponer recursos que no deberían ser de reconocimiento público.

La importancia de estos resultados conforme se fueron implementando otros comandos con mayor nivel de dificultad o de precisión de escaneo, radica en que demuestran la necesidad de ir cada vez más allá de la simple detección de los puertos abiertos cuando de una revisión detallada se requiere como evidencia de la necesidad de un adecuado endurecimiento (*hardening*) de cualquier infraestructura. En razón de que siempre será necesario corregir malas configuraciones a nivel de servidor para mitigar fugas de información y reducir la superficie de ataque. 

Al ser esta la primera vez que llevó a cabo un escaneo de este tipo, toda la información obtenida resultó sumamente interesante. Me gustaría seguir explorando estos comandos con la intención de correlacionar cada uno de los vectores que van apareciendo en escenarios donde se sospecha una posible vulnerabilidad y así saber de qué manera se agudiza la búsqueda de hallazgos con una misión a cumplir en particular. Investigaré más sobre la criptografía, ya que es un área de interés personal. Aplicar lo aprendido más a fondo podría continuar por lo pronto en más entornos seguros y de aprendizaje. 