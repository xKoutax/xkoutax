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
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>
<p>.</p>





























































</body>
</html>
