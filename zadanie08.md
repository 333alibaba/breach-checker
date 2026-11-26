1. odnosnik do nieudanego build-frontend
https://github.com/333alibaba/breach-checker/actions/runs/45807787513

linia bledu :  <h1>Breach Checker
#13 0.169                    ^ Tag must be paired, missing: [ </h1> ], start tag match failed [ <h1> ] on line 14. (tag-pair)

odnosnik do poprawnego wykonania build-frntend: https://github.com/333alibaba/breach-checker/actions/runs/45808823921

wczesniej z rejestru pobierany byl tylko backend, a frontend budowany lokalnie. teraz verify pobiera z rejestru oba obrazy.
----------
2. odnosnik do uruhcomienia z niudanym verify: https://github.com/333alibaba/breach-checker/actions/runs/45809965043

odnosnik do poprawnego uruchomienia: https://github.com/333alibaba/breach-checker/actions/runs/45810753110


po nieudanym uruchomieniu, latest nadal wskazywal na poprawny backend, poniewaz promote zostal pominiety. gdy kots pobiera obraz bez tagu, docker automatycznie uzywa wtedy latest,  czyli poprawna wersje.


-----------------

3. 

tolka@fedora ~/metody_wir/breach-checker $docker pull ghcr.io/333alibaba/breach-checker-backend:latest
latest: Pulling from 333alibaba/breach-checker-backend
83a14706aa6d: Pull complete 
e3e428093f5e: Pull complete 
b6c9f816784e: Pull complete 
ddd0973cc1b8: Pull complete 
286d8c291daa: Pull complete 
a57e6b0ce5b5: Pull complete 
30cf67d7101c: Pull complete 
2deb59953a23: Pull complete 
f50b341628cf: Pull complete 
Digest: sha256:99e2e41cd8b69f4f9219fb7ef67d279689fb0c6b5dea87b91a52cb53bb1f917b
Status: Downloaded newer image for ghcr.io/333alibaba/breach-checker-backend:latest
ghcr.io/333alibaba/breach-checker-backend:latest

//////


tolka@fedora ~/metody_wir/breach-checker $docker pull ghcr.io/333alibaba/breach-checker-backend:$(git log -1 --format=%H)
7378f2a3de92b929f01aa5ad4cf33557810ceb77: Pulling from 333alibaba/breach-checker-backend
Digest: sha256:99e2e41cd8b69f4f9219fb7ef67d279689fb0c6b5dea87b91a52cb53bb1f917b
Status: Downloaded newer image for ghcr.io/333alibaba/breach-checker-backend:7378f2a3de92b929f01aa5ad4cf33557810ceb77
ghcr.io/333alibaba/breach-checker-backend:7378f2a3de92b929f01aa5ad4cf33557810ceb77


/////

tolka@fedora ~/metody_wir/breach-checker $docker images --digests ghcr.io/333alibaba/breach-checker-backend
REPOSITORY                                  TAG                                        DIGEST                                                                    IMAGE ID       CREATED         SIZE
ghcr.io/333alibaba/breach-checker-backend   7378f2a3de92b929f01aa5ad4cf33557810ceb77   sha256:99e2e41cd8b69f4f9219fb7ef67d279689fb0c6b5dea87b91a52cb53bb1f917b   99e2e41cd8b6   3 minutes ago   210MB
ghcr.io/333alibaba/breach-checker-backend   latest                                     sha256:99e2e41cd8b69f4f9219fb7ef67d279689fb0c6b5dea87b91a52cb53bb1f917b   99e2e41cd8b6   3 minutes ago   210MB

////

tolka@fedora ~/metody_wir/breach-checker $docker push ghcr.io/333alibaba/breach-checker-backend:latest
The push refers to repository [ghcr.io/333alibaba/breach-checker-backend]
e3e428093f5e: Layer already exists 
286d8c291daa: Layer already exists 
83a14706aa6d: Layer already exists 
30cf67d7101c: Layer already exists 
a57e6b0ce5b5: Layer already exists 
f50b341628cf: Layer already exists 
ddd0973cc1b8: Layer already exists 
b6c9f816784e: Layer already exists 
2deb59953a23: Layer already exists 
latest: digest: sha256:99e2e41cd8b69f4f9219fb7ef67d279689fb0c6b5dea87b91a52cb53bb1f917b size: 2199


////
Oba digesty sa identyczne, co wskazuje na ten sam obraz. pokazuje to, ze tag jest jedynie wskazniekiem ktory mozna przesuwac.

Pull zadzialal ze wzgledu na zmiane widocznosci z private na public, push wymaga zawsze autoryzacji (tu system przechowal popzednio zapisany token mimo logout)

Token z zakresem write:packeges sluzyl do autoryzowania moich zmian w rejestrze.