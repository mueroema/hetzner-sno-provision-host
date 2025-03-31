# Webserver als Fileserver
``` 
wget -O simple-http-server https://github.com/TheWaWaR/simple-http-server/releases/download/v0.6.10/x86_64-unknown-linux-musl-simple-http-server
chmod +x simple-http-server 
podman build -t quay.io/muellma/webserver:latest .
podman create --rm --name webserver -p 8080:8080 -v .:/www:z quay.io/muellma/webserver:latest
podman start webserver
podman top webserver
podman kill webserver
```