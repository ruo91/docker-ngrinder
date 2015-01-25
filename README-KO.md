# - The Dockerfile of nGrinder
## General Architecture
![nGrinder - General Architecture][1]

Container의 호스트네임은 아래와 같습니다.
- Agent: ngrinder-agent
- Target: ngrinder-target
- Controller: ngrinder-controller

# 0. Github 저장소에서 받아옵니다.
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
적당하게 Agent를 4개 정도 실행 합니다.
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
Target 서버에는 Nginx 웹서버를 사용하며, nGrinder Monitor가 자동 실행 되도록 설정 되어있습니다.
원치 않다면 직접 수정 하시길 바랍니다.
```
# docker build --rm -t ngrinder:target /opt/docker-ngrinder/target
```
#### - Container run
```
# docker run -d --name="ngrinder-target" -h "ngrinder-target" \
--link=ngrinder-controller:ngrinder-controller ngrinder:target
```

# 4. HostOS 설정
#### - Hostname
nginder-controller 컨테이너의 hostname을 HostOS의 /etc/hosts 파일에 추가 합니다.
```
# echo "`docker inspect -f '{{ .NetworkSettings.IPAddress }}' ngrinder-controller`  ngrinder-controller" >> /etc/hosts
```

#### - Nginx
HostOS가 Nginx를 사용하고 있다면, Reverse Proxy를 통해 접근이 가능하도록 추가 해줍니다.
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
설정 반영을 위해 재시작 해줍니다.
```
# nginx -s reload
```

# 5. WEB UI
#### - Login
##### Default ID, PW: admin/admin
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
