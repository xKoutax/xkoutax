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
<p>El archivo PDF Tenia historial de trasferencia, pero nada importante me causo curiosidad la imagen la analizare un poco mas a profundidad</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20010405.png?raw=true" alt="Captura de pantalla">
<p>Dentro de la imagen habia un directorio y un mensaje extraño</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20010517.png?raw=true" alt="Captura de pantalla">
<p>Hemos encontrado una web por donde este tipo ah ido moviendo el dinero</p>
<p>Vamos analizar mas a profunidad esta web</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20010532.png?raw=true" alt="Captura de pantalla">
<p>En el codigo fuente tenemos una cadena de texto encriptada...</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20010626.png?raw=true" alt="Captura de pantalla">
<p>Acabamos de tener una combinacion, esto me hace pensar que es una contraseña intenatre entrar al ordenador del hacker</p>
<pre><code>
  ssh tatsu_@192.168.100.64 
</code></pre>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20010744.png?raw=true" alt="Captura de pantalla">
<p>Podimos accerder, veo que hay algo que dice wallet me causa curiosidad</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20012644.png?raw=true" alt="Captura de pantalla">
<p>Es un archivo corrupto</p>
<h1>Escala de privilegio</h1>
<pre><code>
  sudo -l
</code></pre>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20024959.png?raw=true" alt="Captura de pantalla">
<p>investigamos un poco</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20012738.png?raw=true" alt="Captura de pantalla">
------------------------------------------------------------------------------------------------------------------------------
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20012853.png?raw=true" alt="Captura de pantalla">
<p>al intentar exploatr la vulneravilidad nos saldra un error el cual lo arreglaremos con este comando</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20013006.png?raw=true" alt="Captura de pantalla">
<pre><code>
 export TERM=xterm
</code></pre>
<p>ejecutamos nuestro comando</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20013119.png?raw=true" alt="Captura de pantalla">
<p>Hemos tomado el contro del disposito del atactante </p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20013359.png?raw=true" alt="Captura de pantalla">
<p>Veo nuevamnete un archivo txt llamado wallet, voy a investigar</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20013346.png?raw=true" alt="Captura de pantalla">
<p>al ingresar me da un dominio web ire a ver que es</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20013429.png?raw=true" alt="Captura de pantalla">
<p>Es la wallet del hacker :0 </p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20013439.png?raw=true" alt="Captura de pantalla">
<p>Ahora logramos recuperar los fondos hackeado y enviado a su dueño</p>
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/Captura%20de%20pantalla%202025-03-08%20013447.png?raw=true" alt="Captura de pantalla">
