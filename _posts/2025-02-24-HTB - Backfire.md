---
title: "HTB - Backfiere"
date: 2025-02-24T15:34:30-04:00
categories:
  - HackTheBox
tags:
  - pentesting
---

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HTB - Backfiere</title>
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

<h1>Acompáñame a explotar BackFiere</h1>

<!-- Espacio para la foto -->
<img src="ruta_de_la_imagen.jpg" alt="BackFiere" />

<p>Lo primero que debemos hacer es la fase de reconocimiento con Nmap.</p>

<p>Luego del reconocimiento, podemos notar que hay 3 puertos disponibles, pero el que me llama la atención es el puerto 8000.</p>

<p>Como lo pensé, había una página alojada. Lo que me da curiosidad es que, como podemos ver, hay 2 archivos, los cuales procederé a descargar.</p>

<p>Estos archivos me dan información valiosa:</p>

<pre><code>
havoc.yaotl

Teamserver {
    Host = "127.0.0.1"
    Port = 40056

    Build {
        Compiler64 = "data/x86_64-w64-mingw32-cross/bin/x86_64-w64-mingw32-gcc"
        Compiler86 = "data/i686-w64-mingw32-cross/bin/i686-w64-mingw32-gcc"
        Nasm = "/usr/bin/nasm"
    }
}

Operators {
    user "ilya" {
        Password = "CobaltStr1keSuckz!"
    }

    user "sergej" {
        Password = "1w4nt2sw1tch2h4rdh4tc2"
    }
}

Demon {
    Sleep = 2
    Jitter = 15

    TrustXForwardedFor = false

    Injection {
        Spawn64 = "C:\\Windows\\System32\\notepad.exe"
        Spawn32 = "C:\\Windows\\SysWOW64\\notepad.exe"
    }
}

Listeners {
    Http {
        Name = "Demon Listener"
        Hosts = [
            "backfire.htb"
        ]
        HostBind = "127.0.0.1" 
        PortBind = 8443
        PortConn = 8443
        HostRotation = "round-robin"
        Secure = true
    }
}
</code></pre>

<pre><code>
disable_tls.patch

Disable TLS for WebSocket management port 40056, so I can prove that
sergej is not doing any work.
Management port only allows local connections (we use SSH forwarding) so 
this will not compromise our teamserver.

diff --git a/client/src/Havoc/Connector.cc b/client/src/Havoc/Connector.cc
index abdf1b5..6be76fb 100644
--- a/client/src/Havoc/Connector.cc
+++ b/client/src/Havoc/Connector.cc
@@ -8,12 +8,11 @@ Connector::Connector( Util::ConnectionInfo* ConnectionInfo )
 {
     Teamserver   = ConnectionInfo;
     Socket       = new QWebSocket();
-    auto Server  = "wss://" + Teamserver->Host + ":" + this->Teamserver->Port + "/havoc/";
+    auto Server  = "ws://" + Teamserver->Host + ":" + this->Teamserver->Port + "/havoc/";
     auto SslConf = Socket->sslConfiguration();

     /* ignore annoying SSL errors */
     SslConf.setPeerVerifyMode( QSslSocket::VerifyNone );
-    Socket->setSslConfiguration( SslConf );
     Socket->ignoreSslErrors();

     QObject::connect( Socket, &QWebSocket::binaryMessageReceived, this, [&]( const QByteArray& Message )

diff --git a/teamserver/cmd/server/teamserver.go b/teamserver/cmd/server/teamserver.go
index 9d1c21f..59d350d 100644
--- a/teamserver/cmd/server/teamserver.go
+++ b/teamserver/cmd/server/teamserver.go
@@ -151,7 +151,7 @@ func (t *Teamserver) Start() {
    }

    // start the teamserver
-   if err = t.Server.Engine.RunTLS(Host+":"+Port, certPath, keyPath); err != nil {
+   if err = t.Server.Engine.Run(Host+":"+Port); err != nil {
      logger.Error("Failed to start websocket: " + err.Error())
    }
</code></pre>

<!-- Espacio para scripts en Ruby -->
<%# Código Ruby aquí %>
<% ruby_script = "puts 'Ejecutando script en Ruby'" %>
<%= ruby_script %>

</body>
</html>
