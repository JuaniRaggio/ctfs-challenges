# Estas fueron las rtas que obtuve

```sh
(base) juaniraggio@oogway http_user_agent % curl http://challenge01.root-me.org/web-serveur/ch5/ -v
* Host challenge01.root-me.org:80 was resolved.
* IPv6: (none)
* IPv4: 212.129.38.224
*   Trying 212.129.38.224:80...
* Connected to challenge01.root-me.org (212.129.38.224) port 80
> GET /web-serveur/ch5/ HTTP/1.1
> Host: challenge01.root-me.org
> User-Agent: curl/8.7.1
> Accept: */*
>
* Request completely sent off
< HTTP/1.1 200 OK
< Server: nginx
< Date: Sat, 07 Nov 2026 06:55:25 GMT
< Content-Type: text/html; charset=UTF-8
< Transfer-Encoding: chunked
< Connection: keep-alive
< Vary: Accept-Encoding
< Header-RootMe-Admin: none
<
<html>
<body><link rel='stylesheet' property='stylesheet' id='s' type='text/css' href='/template/s.css' media='all' /><iframe id='iframe' src='https://www.root-me.org/?page=externe_header'></iframe>
<p>Content is not the only part of an HTTP response!</p>
</body>
</html>
* Connection #0 to host challenge01.root-me.org left intact
(base) juaniraggio@oogway http_user_agent % curl http://challenge01.root-me.org/web-serveur/ch5/ -v -H "header-rootme-admin: yes"
* Host challenge01.root-me.org:80 was resolved.
* IPv6: (none)
* IPv4: 212.129.38.224
*   Trying 212.129.38.224:80...
* Connected to challenge01.root-me.org (212.129.38.224) port 80
> GET /web-serveur/ch5/ HTTP/1.1
> Host: challenge01.root-me.org
> User-Agent: curl/8.7.1
> Accept: */*
> header-rootme-admin: yes
>
* Request completely sent off
< HTTP/1.1 200 OK
< Server: nginx
< Date: Sat, 07 Nov 2026 06:56:07 GMT
< Content-Type: text/html; charset=UTF-8
< Transfer-Encoding: chunked
< Connection: keep-alive
< Vary: Accept-Encoding
< Header-RootMe-Admin: none
<
<html>
<body><link rel='stylesheet' property='stylesheet' id='s' type='text/css' href='/template/s.css' media='all' /><iframe id='iframe' src='https://www.root-me.org/?page=externe_header'></iframe>
<p>Content is not the only part of an HTTP response!</p>
<p>You dit it ! You can validate the challenge with the password HeadersMayBeUseful
</p></body>
</html>
* Connection #0 to host challenge01.root-me.org left intact
```
