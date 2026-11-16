1. ============================
backend:prod bez --ignore-unfixed:
Total: 49 (HIGH: 49, CRITICAL: 0)

backend:prod z --ignore-unfixed:
Total: 2 (HIGH: 2, CRITICAL: 0)

frontend:prod bez --ignore-unfixed:
Total: 3 (HIGH: 3, CRITICAL: 0)

frontend:prod z --ignore-unfixed:
Total: 3 (HIGH: 3, CRITICAL: 0)

// przed --pull
--ignore
backend:prod : Total: 2 (HIGH: 2, CRITICAL: 0)
frontend:prod: Total: 3 (HIGH: 3, CRITICAL: 0)


brak--ignore
backend:prod: Total: 49 (HIGH: 49, CRITICAL: 0)
frontend:prod: Total: 3 (HIGH: 3, CRITICAL: 0)

--pull nie zmienil wyniku

2.================

backend-1  | FileNotFoundError: [Errno 2] No usable temporary directory found in ['/tmp', '/var/tmp', '/usr/tmp', '/app']

tolka@fedora ~/metody_wir/breach-checker $docker compose exec backend touch /etc/test
touch: cannot touch '/etc/test': Read-only file system

tolka@fedora ~/metody_wir/breach-checker $docker compose exec backend touch /tmp/test

tolka@fedora ~/metody_wir/breach-checker $docker compose exec backend grep CapEff /proc/1/status
CapEff:	0000000000000000

tolka@fedora ~/metody_wir/breach-checker $docker compose exec backend pytest
=============================== test session starts ================================
platform linux -- Python 3.12.16, pytest-8.4.2, pluggy-1.7.0
rootdir: /app
collected 7 items                                                                  

tests/test_check.py .......                                                  [100%]

================================ 7 passed in 0.06s =================================
tolka@fedora ~/metody_wir/breach-checker $

/////

read_only: true zabezpiecza system plików kontenera przed modyfikacja,  cap_drop: ALL odbiera procesowi dodatkowe uprawnienia jądra Linux, a no-new-privileges:true uniemożliwia uzyskanie wyższych uprawnień podczas działania procesu. read_only ogranicza możliwość zapisu do systemu plików, a USER appuser określa użytkownika

3.===============

tolka@fedora ~/lab07 $docker history sekret-demo:plik
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
c220e4b9cd30   49 seconds ago   RUN /bin/sh -c rm /tmp/klucz.txt # buildkit     0B        buildkit.dockerfile.v0
<missing>      49 seconds ago   RUN /bin/sh -c test -s /tmp/klucz.txt && ech…   0B        buildkit.dockerfile.v0
<missing>      50 seconds ago   COPY klucz.txt /tmp/klucz.txt # buildkit        4.1kB     buildkit.dockerfile.v0
<missing>      10 days ago      CMD ["/bin/sh"]                                 0B        buildkit.dockerfile.v0
<missing>      10 days ago      ADD alpine-minirootfs-3.22.8-x86_64.tar.gz /…   9.94MB    buildkit.dockerfile.v0

////

olka@fedora ~/lab07 $for f in obraz/blobs/sha256/*; do
  tar -tf "$f" 2>/dev/null | grep -qx 'tmp/klucz.txt' && tar -xOf "$f" tmp/klucz.txt 
done
tajny-klucz-<tola>

/////

RUN rm nie usunęło sekretu z całego obrazu, ponieważ każda instrukcja Dockerfile tworzy osobną warstwę. Plik został w poprzedniej warstwie utworzonej przez COPY i można go odzyskać za pomocą docker save. Bezpiecznym sposobem przekazania sekretu podczas budowania jest użycie --secret oraz RUN --mount=type=secret, dzięki czemu sekret nie zostaje zapisany w warstwach obrazu.
