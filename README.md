# - The Dockerfile of nGrinder
## General Architecture
![nGrinder - General Architecture][1]

Host name of each containers.
- Agent: ngrinder-agent
- Target: ngrinder-target
- Controller: ngrinder-controller

# 0. Clone of github repository
```
# git clone https://github.com/ruo91/docker-ngrinder /opt
```
# 1. Controller
#### - Build
```
# docker build --rm -t ngrinder:controller /opt/docker-ngrinder/controller
```
#### - Container run
```
# docker run -d --name="ngrinder-controller" -h "ngrinder-controller" ngrinder:controller
```

# 2. Agent
#### - Build
```
# docker build --rm -t ngrinder:agent /opt/docker-ngrinder/agent
```
#### - Container run
Run four containers.
```
# docker run -d --name="ngrinder-agent-1" -h "ngrinder-agent-1" \
--link=ngrinder-controller:ngrinder-controller ngrinder:agent
```
```
# docker run -d --name="ngrinder-agent-2" -h "ngrinder-agent-2" \
--link=ngrinder-controller:ngrinder-controller ngrinder:agent
```
```
# docker run -d --name="ngrinder-agent-3" -h "ngrinder-agent-3" \
--link=ngrinder-controller:ngrinder-controller ngrinder:agent
```
```
# docker run -d --name="ngrinder-agent-4" -h "ngrinder-agent-4" \
--link=ngrinder-controller:ngrinder-controller ngrinder:agent
```

# 3. Target (Nginx + nGrinder Monitor)
#### - Build
I use Nginx as a web server.
```
# docker build --rm -t ngrinder:target /opt/docker-ngrinder/target
```
#### - Container run
```
# docker run -d --name="ngrinder-target" -h "ngrinder-target" \
--link=ngrinder-controller:ngrinder-controller ngrinder:target
```

# 4. Setting up HostOS
#### - Hostname
```
# echo "`docker inspect -f '{{ .NetworkSettings.IPAddress }}' ngrinder-controller`  ngrinder-controller" >> /etc/hosts
```

#### - Nginx
```
# nano /etc/nginx/nginx.conf
```
```
## Nginx ##
user nginx;
pid logs/nginx.pid;
error_log logs/error.log;
access_log off;

worker_processes 2;
events {
    worker_connections 1024;
    use epoll;
}

http {
    include mime.types;
    default_type application/octet-stream;
    types_hash_max_size 2048;
    server_names_hash_bucket_size 64;
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
    '$status $body_bytes_sent "$http_referer" '
    '"$http_user_agent" "$http_x_forwarded_for"';

    ## TCP options
    tcp_nodelay on;
    tcp_nopush on;

    # Virtualhost
    server {
        listen  80;
        server_name ngrinder.example.com;
        location / {
            proxy_set_header Host $host;
            proxy_set_header X-Forwarded-Host $host;
            proxy_set_header X-Forwarded-Server $host;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass http://ngrinder-controller:80;
            client_max_body_size 10M;
        }
    }
}
```
#### - Nginx restart
```
# nginx -s reload
```

# 6. WEB UI
#### - Login
![Login][2]

#### - Home
![Home][3]

#### - Agent Management
![Agent Management][4]

#### - Agent Management2
![Agent Management2][5]

#### - Quick Start
##### Test of ngrinder-target
![Quick Start][6]

#### - Quick Start2
##### Test Configuration
![Quick Start2][7]

#### - Schedule Setting
![Schedule Setting][8]

#### - Performance Test
![Performance Test][9]

#### - Complete of performance test
![Performance Test2][10]

#### - Performance Test - Detail Report
![Performance Test - Detail Report][11]

Thanks. :-)
[1]: http://cdn.yongbok.net/ruo91/img/nGrinder/nGrinder-Architecture.png
[2]: http://cdn.yongbok.net/ruo91/img/nGrinder/nGrinder-0.png
[3]: http://cdn.yongbok.net/ruo91/img/nGrinder/nGrinder-1.png
[4]: http://cdn.yongbok.net/ruo91/img/nGrinder/nGrinder-2.png
[5]: http://cdn.yongbok.net/ruo91/img/nGrinder/nGrinder-3.png
[6]: http://cdn.yongbok.net/ruo91/img/nGrinder/nGrinder-4.png
[7]: http://cdn.yongbok.net/ruo91/img/nGrinder/nGrinder-5.png
[8]: http://cdn.yongbok.net/ruo91/img/nGrinder/nGrinder-6.png
[9]: http://cdn.yongbok.net/ruo91/img/nGrinder/nGrinder-7.png
[10]: http://cdn.yongbok.net/ruo91/img/nGrinder/nGrinder-8.png
[11]: http://cdn.yongbok.net/ruo91/img/nGrinder/nGrinder-9.png