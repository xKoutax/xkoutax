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





























































































</body>
</html>
