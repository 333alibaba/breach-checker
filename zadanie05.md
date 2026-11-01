1. [+] up 6/6
 ✔ Image breach-checker-frontend       Built                                    1.6s
 ✔ Image breach-checker-backend        Built                                    1.5s
 ✔ Network breach-checker_default      Created                                  0.3s
 ✔ Container breach-checker-db-1       Healthy                                  6.8s
 ✔ Container breach-checker-backend-1  Healthy                                 37.4s
 ✔ Container breach-checker-frontend-1 Started
 ##########
 NAME                        IMAGE                     COMMAND                  SERVICE    CREATED         STATUS                   PORTS
breach-checker-backend-1    breach-checker-backend    "python app.py"          backend    5 minutes ago   Up 5 minutes (healthy)   5000/tcp
breach-checker-db-1         postgres:16-alpine        "docker-entrypoint.s…"   db         5 minutes ago   Up 5 minutes (healthy)   5432/tcp
breach-checker-frontend-1   breach-checker-frontend   "/docker-entrypoint.…"   frontend   5 minutes ago   Up 5 minutes             0.0.0.0:8080->80/tcp, [::]:8080->80/tcp
##########
$curl -X POST http://localhost:8080/api/check -H "Content-Type: application/json" -d '{"hash":"bc45408273d7e1914b741c3407b0b559764f7876"}'
{"found":true}
tolka@fedora ~/metody_wir/breach-checker $docker compose down
[+] down 4/4
 ✔ Container breach-checker-frontend-1 Removed                                  0.6s
 ✔ Container breach-checker-backend-1  Removed                                 10.5s
 ✔ Container breach-checker-db-1       Removed                                  0.3s
 ✔ Network breach-checker_default      Removed                                  0.3s
tolka@fedora ~/metody_wir/breach-checker $docker compose up -d
[+] up 4/4
 ✔ Network breach-checker_default      Created                                  0.3s
 ✔ Container breach-checker-db-1       Healthy                                  6.5s
 ✔ Container breach-checker-backend-1  Healthy                                 37.1s
 ✔ Container breach-checker-frontend-1 Started                                 37.6s
tolka@fedora ~/metody_wir/breach-checker $curl -X POST http://localhost:8080/api/check -H "Content-Type: application/json" -d '{"hash":"bc45408273d7e1914b741c3407b0b559764f7876"}'
{"found":true}
tolka@fedora ~/metody_wir/breach-checker $docker compose down -v
[+] down 5/5
 ✔ Container breach-checker-frontend-1 Removed                                  0.6s
 ✔ Container breach-checker-backend-1  Removed                                 11.0s
 ✔ Container breach-checker-db-1       Removed                                  0.4s
 ✔ Volume breach-checker_pgdata        Removed                                  0.1s
 ✔ Network breach-checker_default      Removed                                  0.4s
tolka@fedora ~/metody_wir/breach-checker $docker compose up -d
[+] up 5/5
 ✔ Network breach-checker_default      Created                                  0.2s
 ✔ Volume breach-checker_pgdata        Created                                  0.1s
 ✔ Container breach-checker-db-1       Healthy                                  6.6s
 ✔ Container breach-checker-backend-1  Healthy                                 37.1s
 ✔ Container breach-checker-frontend-1 Started                                 37.5s
tolka@fedora ~/metody_wir/breach-checker $curl -X POST http://localhost:8080/api/check -H "Content-Type: application/json" -d '{"hash":"bc45408273d7e1914b741c3407b0b559764f7876"}'
{"found":false}
===========
Po samym down kontener zostal usuniety ale wolumen zostal, dopiero po dodaniu -v wolumen pgdata zostal usuniety. Przy kolejnym uruchomieniu wykonalo sie init.sql i wczytane zostalo pierwotne 89 hashy.

#################
2. 
tolka@fedora ~/metody_wir/breach-checker $docker compose ps
NAME                        IMAGE                     COMMAND                  SERVICE    CREATED          STATUS                    PORTS
breach-checker-backend-1    breach-checker-backend    "python app.py"          backend    13 minutes ago   Up 13 minutes (healthy)   5000/tcp
breach-checker-backend-2    breach-checker-backend    "python app.py"          backend    48 seconds ago   Up 47 seconds (healthy)   5000/tcp
breach-checker-backend-3    breach-checker-backend    "python app.py"          backend    48 seconds ago   Up 46 seconds (healthy)   5000/tcp
breach-checker-db-1         postgres:16-alpine        "docker-entrypoint.s…"   db         13 minutes ago   Up 13 minutes (healthy)   5432/tcp
breach-checker-frontend-1   breach-checker-frontend   "/docker-entrypoint.…"   frontend   13 minutes ago   Up 12 minutes             0.0.0.0:8080->80/tcp, [::]:8080->80/tcp
#############
tolka@fedora ~/metody_wir/breach-checker $docker compose exec frontend nslookup backend
Server:		127.0.0.11
Address:	127.0.0.11:53

Non-authoritative answer:

Non-authoritative answer:
Name:	backend
Address: 172.18.0.5
Name:	backend
Address: 172.18.0.3
Name:	backend
Address: 172.18.0.6
###########
przed restartem (wszytskie obsluguje backend-1):
backend-1  | check: hash=7110e...
backend-1  | 172.18.0.4 - - [01/Nov/2026 22:17:35] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=420df...
backend-1  | 172.18.0.4 - - [01/Nov/2026 22:17:39] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=8cb22...
backend-1  | 172.18.0.4 - - [01/Nov/2026 22:17:43] "POST /check HTTP/1.1" 200 -
po restarcie :
backend-2  | 172.18.0.4 - - [01/Nov/2026 22:23:06] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.18.0.4 - - [01/Nov/2026 22:23:06] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.18.0.4 - - [01/Nov/2026 22:23:06] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.18.0.4 - - [01/Nov/2026 22:23:06] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.18.0.4 - - [01/Nov/2026 22:23:06] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.18.0.4 - - [01/Nov/2026 22:23:06] "POST /check HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-3  | 172.18.0.4 - - [01/Nov/2026 22:23:06] "POST /check HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-3  | 172.18.0.4 - - [01/Nov/2026 22:23:06] "POST /check HTTP/1.1" 200 -
##########
Poniewaz nginx pamietal adres backend z osttaniego uruchomienia, po restarcie poznal kolejne 2. Bo kazdy z 3 replik probowalby zajac port 5000.
#########

