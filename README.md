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
  * [NGINX server]
    * [Reverse proxy configuration]
  * [Setup vagrant]
  * [Setup folders and required files]
  * [Vagrantfiles]
* [Testing]
* [Project review]

## Concept
### Requierements

* [Vagrant](https://www.vagrantup.com/downloads.html)
* [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
* [Git](https://git-scm.com/download/win)

### Docker Network
Network diagram between Docker Network and Physical Network.
![Docker Network 1](./Docker-Network.png)

Network diagram between NGINX Reverse Proxy and Docker Ports.
![Docker Network 2](./Docker-Network2.png)

### IP-Table
| Docker / Host | IP-Address | Ports | Description |
| ------------- | ---------- | ----- | ----------- |
| Owncloud | 172.18.0.2 /16 | TCP 8080, TCP 4000 | Owncloud Docker |
| Maria_DB | 172.18.0.3 /16 | TCP 3306 | Maria SQL Docker |
| Redis | 172.18.0.4 /16 | TCP 6379 | Redis Docker |
| Nitinankeel.ch | 172.18.0.1 /16 | - | Docker-Bridge |
| Nitinankeel.ch | 185.101.157.122 /24 | TCP 443, 80 | Reverse proxy redirection |

### Reverse proxy
The reverse proxy wil be configured to redirect all traffic from cloud.nitinankeel.ch to the Owncloud Docker-Container 172.18.0.2 on port 4000.
| From-Address | From-Port | To-Address | To-Port | Description |
| ------------ | --------- | ---------- | ------- | ----------- |
| cloud.nitinankeel.ch | 443 | 172.18.0.2 | 4000 | HTTPS to Docker |
| cloud.nitinankeel.ch | 80 | cloud.nitinankeel.ch | 443 | HTTP to HTTPS redirect |