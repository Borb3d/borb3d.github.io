# Lame WriteUp
Volver al [Indice](README.md)

Comenzamos con la primera máquina que voy a realizar su WriteUp en HTB, en este caso estaremos ante la máquina "Lame", creada por "Ch4p", tiene una dificultad de 2.6 sobre 10 y una puntuación de 4.4 sobre 5...  
La verdad que siemrpe había escuchado que era una máquina muy sencilla y pensé que era porque tenía una vulnerabilidad en Metasploit que te facilitaba la tarea, pero, sin Metasploit ha resultado igual de sencilla sinceramente, aunque muy buena para conocer conceptos.

----------------------------------------------------------------------------------------------------------------------------------------------------------------------
## *# Enumeración*
Comenzamos realizando una enumeración básica del sistema con nmap.

[nmap1](images/htb/lame/nmap1.png)
[nmap2](images/htb/lame/nmap2.png)

Como vemos, tiene bastantes puertos abiertos, así que vamos a ir enumerándolos.
* Puerto 21 => Tiene un FTP con acceso anónimo habilitado pero no contiene nada (tiene una vulnerabilidad en Metasploit si buscamos con ```"searchsploit"```)
* Puerto 22 => Un puerto de SSH con la versión  4.7 (también con alguna que otra vulnerabilidad con Metasploit, y sin él con autenticación).
* Puerto 139 y 445 => Aquí encontramos samba, es un servicio de transferencia de archivos, aquí buscamos por Google y encontramos un exploit en Python para la versión 3.0.20, que es la que tenemos.
  * Dejo por aquí enlace a la página donde encontré el exploit.
  * [smb-3.0.20](https://github.com/macha97/exploit-smb-3.0.20/blob/master/exploit-smb-3.0.20.py)

pd: Decir que todo lo que se puede realizar con Metasploit lo podemos realizar manualmente, sea más dificil o no, pero como estuve enumerando por encima primero para ver con lo que me encontraba no saqué la clave para los 2 puertos anteriores.

Ahora si, con este exploit en Python lo abrimos y vemos que tiene el código de una reverse shell generada con msfvenom, copiamos el comando y lo adaptamos a nuestra máquina.

[exploit&comando](images/htb/lame/exploitYcomando.png)

## *# Explotación*
Una vez completado el exploit con el código de nuestra reverse, lo lanzamos y increíblemente hemos accedido a la máquina directamente como ROOT, así que...  
Una vez más, ¡Somos ROOT!

[exploit&flags](images/htb/lame/exploitYflags.png)