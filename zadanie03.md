~/metody_wir/breach-checker $docker inspect --format '{{json .Config.Labels}}' anatola-dwornik-frontend:latest
{"author":"Anatola Dwornik","data":"2026-10-15","maintainer":"anatola_dwornik"}

~/metody_wir/breach-checker $docker inspect --format '{{json .Config.Labels}}' anatola-dwornik-frontend:v1
{"author":"Anatola Dwornik","data":"2026-10-15","maintainer":"anatola_dwornik"}

###################

~/metody_wir/breach-checker $docker history anatola-dwornik-frontend:v1
IMAGE          CREATED       CREATED BY                                      SIZE      COMMENT
23f0057be631   6 days ago    EXPOSE [80/tcp]                                 0B        buildkit.dockerfile.v0
<missing>      6 days ago    RUN /bin/sh -c htmlhint /usr/share/nginx/htm…   0B        buildkit.dockerfile.v0
<missing>      6 days ago    COPY . /usr/share/nginx/html # buildkit         12.3kB    buildkit.dockerfile.v0
<missing>      6 days ago    RUN /bin/sh -c apk add --no-cache nodejs npm…   99.3MB    buildkit.dockerfile.v0
<missing>      6 days ago    LABEL author=Anatola Dwornik data=2026-10-15    0B        buildkit.dockerfile.v0
<missing>      6 days ago    LABEL maintainer=anatola_dwornik                0B        buildkit.dockerfile.v0
<missing>      4 weeks ago   RUN /bin/sh -c set -x     && apkArch="$(cat …   52.8MB    buildkit.dockerfile.v0
<missing>      4 weeks ago   ENV ACME_VERSION=0.4.1                          0B        buildkit.dockerfile.v0
<missing>      4 weeks ago   ENV NJS_RELEASE=1                               0B        buildkit.dockerfile.v0
<missing>      4 weeks ago   ENV NJS_VERSION=1.0.1                           0B        buildkit.dockerfile.v0
<missing>      4 weeks ago   CMD ["nginx" "-g" "daemon off;"]                0B        buildkit.dockerfile.v0
<missing>      4 weeks ago   STOPSIGNAL SIGQUIT                              0B        buildkit.dockerfile.v0
<missing>      4 weeks ago   EXPOSE map[80/tcp:{}]                           0B        buildkit.dockerfile.v0
<missing>      4 weeks ago   ENTRYPOINT ["/docker-entrypoint.sh"]            0B        buildkit.dockerfile.v0
<missing>      4 weeks ago   COPY 30-tune-worker-processes.sh /docker-ent…   8.19kB    buildkit.dockerfile.v0
<missing>      4 weeks ago   COPY 20-envsubst-on-templates.sh /docker-ent…   4.1kB     buildkit.dockerfile.v0
<missing>      4 weeks ago   COPY 15-local-resolvers.envsh /docker-entryp…   4.1kB     buildkit.dockerfile.v0
<missing>      4 weeks ago   COPY 10-listen-on-ipv6-by-default.sh /docker…   4.1kB     buildkit.dockerfile.v0
<missing>      4 weeks ago   COPY docker-entrypoint.sh / # buildkit          4.1kB     buildkit.dockerfile.v0
<missing>      4 weeks ago   RUN /bin/sh -c set -x     && addgroup -g 101…   5.53MB    buildkit.dockerfile.v0
<missing>      4 weeks ago   ENV DYNPKG_RELEASE=1                            0B        buildkit.dockerfile.v0
<missing>      4 weeks ago   ENV PKG_RELEASE=1                               0B        buildkit.dockerfile.v0
<missing>      4 weeks ago   ENV NGINX_VERSION=1.31.6                        0B        buildkit.dockerfile.v0
<missing>      4 weeks ago   LABEL maintainer=NGINX Docker Maintainers <d…   0B        buildkit.dockerfile.v0
<missing>      4 weeks ago   CMD ["/bin/sh"]                                 0B        buildkit.dockerfile.v0
<missing>      4 weeks ago   ADD alpine-minirootfs-3.24.2-x86_64.tar.gz /…   10.1MB    buildkit.dockerfile.v0

#####################

Warstwy obrazu bazowego na dole (4tygodnie temu), moj dockerfile widoczny od gory. Warstwy 0B nie dodaja/kopiuja plikow, a mainteiner zostal nadpisany 6 dni temu.

#############################

frontend1 : Wed Oct 21 20:34:26 UTC 2026
frontend2: Wed Oct 21 20:34:26 UTC 2026

CONTAINER ID   IMAGE                         COMMAND                  CREATED              STATUS              PORTS                                     NAMES
30e1b20068e9   anatola-dwornik-frontend:v1   "/docker-entrypoint.…"   52 seconds ago       Up 52 seconds       0.0.0.0:8082->80/tcp, [::]:8082->80/tcp   frontend2
15cca32954f2   anatola-dwornik-frontend:v1   "/docker-entrypoint.…"   About a minute ago   Up About a minute   0.0.0.0:8081->80/tcp, [::]:8081->80/tcp   frontend1

Czas jest inny niz uruchomienia bo run wykonalo sie przy budowaniu obrazow, a oba kontenery korzystaja z tego samego obrazu.

##############################

location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

jest to domyslny katalog ktory zwraca pliki strony, dlatego do niego je kopiujemy

########################

~/metody_wir/breach-checker/frontend $docker exec blad ls /usr/share/nginx
html
index.html
script.js
style.css
~/metody_wir/breach-checker/frontend $docker exec blad ls /usr/share/nginx/html
50x.html
build.txt
index.html

build sie udal poniewaz polecenie jest poprawne, a htmlhint sprawdzil domyslna strone nginx, dlatego ze moja jej nie zastapila tylko traila do innego katalogu. 

#####################

~/metody_wir/breach-checker/frontend $docker rmi breach-checker-frontend:latest
Untagged: breach-checker-frontend:latest

~/metody_wir/breach-checker/frontend $docker rmi breach-checker-frontend:v2
Untagged: breach-checker-frontend:v2
Deleted: sha256:3363ea77ee00fb0a5b0a9e0f7266cfd69e66896fe4d9df66049769a6cb41ab91

rmi usuwa obraz z dysku, kiedy nie ma juz innych zaleznosci z tym obrazem np. inne tagi


