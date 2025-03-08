---
title: "Forentest"
date: 2025-03-08T01:48:48-05:00
categories:
  - universidad
tags:
  - pentesting
  - easy
---
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Forentest</title>
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

<h1>Acompañame en este nuevo reto Forentest</h1>
<!-- Espacio para la foto -->
<div style="text-align: center; margin: 20px;">
  <img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20003854.png?raw=true" 
       alt="Captura de pantalla" 
       style="max-width: 100%; height: auto; border-radius: 8px;" />
  <p>Forentest</p>
  </div>
<p>Comenzamos con el renococimiento de puertos Con nmap.</p>
<pre><code>
  sudo nmap -p- -sS --open --min-rate 5000 -vvv -n -Pn 192.168.100.64 -oG Allports
</code></pre>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20003654.png?raw=true" alt="Captura de pantalla">
<p>Hacemos un analisis mas profundo en los puertos.</p>
<pre><code>
   sudo nmap -p22,8000,65535 -sCV -Pn 192.168.100.64
</code></pre>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20003701.png?raw=true" alt="Captura de pantalla">
<p>Ingresamos a un sitio donde nos muestras las Transferencias que el hacker realizo.</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20003835.png?raw=true" alt="Captura de pantalla">
--------------------------------------------------------------------------------------------------------------
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20003840.png?raw=true" alt="Captura de pantalla">
<p>El hacker accedio y realizo la tranferencia, no solo eso cambio la claves de su vicitima.</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20003854.png?raw=true" alt="Captura de pantalla">
<p>Procedemos a buscar pista que nos puedena llevar a la detencion de este tipo.</p>
<p>verificaremos el codigo fuente a ver si no dejo algo.</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20004030.png?raw=true" alt="Captura de pantalla">
<p>Desencriptamos el codigo y nos muestra un mensaje.</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20004102.png?raw=true" alt="Captura de pantalla">
<p>Hmmmm, cambio el usuairo el que nuestro cliente tenia era juan.perez y ahora solo es perez.</p>
<p>con esta informacion se me ocurre hacer un ataque de fuerza bruta.</p>
<pre><code>
   hydra -l perez -P mini_rockyou.txt ssh://192.168.100.64 -t 4
</code></pre>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20005404.png?raw=true" alt="Captura de pantalla">
<p>Genial.</p>
<p>tenemos las credenciales completas intentaremos acceder.</p>
<pre><code>
  ssh perez@192.168.100.64 
</code></pre>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20005453.png?raw=true" alt="Captura de pantalla">
<p>Ahora tenemos mas informacion, podemos ver un id diferente, la cual puede tratarse del hacker</p>
<p>Hay un archivo pdf y un archivo png, lo enviare a mi escritorio para analizarlos</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20010023.png?raw=true" alt="Captura de pantalla">
<p>PDF</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20010150.png?raw=true" alt="Captura de pantalla">
<p>PNG</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20010226.png?raw=true" alt="Captura de pantalla">
