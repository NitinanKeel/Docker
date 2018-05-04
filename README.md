# Docker
Nitinan Keel </br>
Containerization of application and services

## Table of contents
* [Concept]
  * [Requierements]
  * [Docker Network]
  * [IP-Table]
  * [Reverse proxy]
* [Installation]
  * [Setup external server]
  * [NGINX service]
    * [Reverse proxy configuration]
    * [SSL with Let's Encrypt]
  * [Docker-Compose File]
* [Testing]
* [Project review]

## Concept
### Requierements

* [Ubuntu Server 16.04](https://www.ubuntu.com/download/server)
* [Plain NGINX installation](https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-open-source/)
* [Docker CE](https://store.docker.com/editions/community/docker-ce-server-ubuntu)
* [Docker-compose](https://docs.docker.com/compose/)

### Docker Network
Network diagram between Docker Network and Physical Network.

![Docker Network 1](./Docker-Network.png)

Network diagram between NGINX Reverse Proxy and Docker Ports.

![Docker Network 2](./Docker-network2.png)

### IP-Table
| Docker / Host | IP-Address | Ports | Description |
| ------------- | ---------- | ----- | ----------- |
| Owncloud | 172.18.0.2 /16 | TCP 8080, TCP 4000 | Owncloud Docker |
| Maria_DB | 172.18.0.3 /16 | TCP 3306 | Maria SQL Docker |
| Redis | 172.18.0.4 /16 | TCP 6379 | Redis Docker |
| Nitinankeel.ch | 172.18.0.1 /16 | - | Docker-Bridge |
| Nitinankeel.ch | 185.101.157.122 /24 | TCP 443, 80 | Reverse proxy redirection |

### Reverse proxy
| From-Address | From-Port | To-Address | To-Port | Description |
| ------------ | --------- | ---------- | ------- | ----------- |
| cloud.nitinankeel.ch | 443 | 172.18.0.2 | 4000 | HTTPS to Docker |
| cloud.nitinankeel.ch | 80 | cloud.nitinankeel.ch | 443 | HTTP to HTTPS redirect |

## Installation
### Setup external server
I'm using the UFW on my Server. In order to accept HTTP and HTTPS traffic with a firewall, you need to open those ports.
Just run these commands:
```sh
sudo ufw allow HTTP
sudo ufw allow HTTPS
sudo ufw reload
```
Now we need to configure the NGINX service.

### NGINX service
Because I have multiple services, which needs the ports 80 and 443, I'll setup a reverse proxy with NGINX.

#### Reverse proxy configuration
In my network diagram above, you can see, that the reverse proxy will listen for incomming traffic on cloud.nitinankeel.ch and will then redirect it to my docker container.

Just add a new site in your NGINX config folder (``/etc/nginx/sites-available/``):

```BASH
vim /etc/nginx/sites-available/cloud.nitinankeel.ch
```

Paste this in:
```NGINX   
server {
	server_name www.cloud.nitinankeel.ch; # Change this
	return 301 $scheme://cloud.nitinankeel.ch$request_uri; # Change this
}
server { 
        index index.php index.html index.htm;
        server_name cloud.nitinankeel.ch; # Change this
        location / {
        proxy_pass http://127.0.0.1:8080;
        }

         location ~ /\.ht {
                deny all;
        }
}
server {
        listen 80;
}
```
Save and close.

#### SSL with Let's Encrypt
Now the proxy is just listenning to HTTP.
To enable HTTPS, [install](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-16-04) the Certbot Agent.

### Docker-Compose File
