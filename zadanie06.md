1================
 ~/metody_wir/breach-checker $docker compose exec backend pytest -v
=============================== test session starts ================================
platform linux -- Python 3.12.16, pytest-8.4.2, pluggy-1.7.0 -- /usr/local/bin/python3.12
cachedir: .pytest_cache
rootdir: /app
collected 7 items                                                                  

tests/test_check.py::test_leaked_hash_is_found PASSED                        [ 14%]
tests/test_check.py::test_unknown_hash_is_not_found PASSED                   [ 28%]
tests/test_check.py::test_missing_hash_field PASSED                          [ 42%]
tests/test_check.py::test_plaintext_password_is_rejected PASSED              [ 57%]
tests/test_check.py::test_upper_is_found PASSED                              [ 71%]
tests/test_check.py::test_hash_too_short_rejected PASSED                     [ 85%]
tests/test_check.py::test_hash_invalid_char_rejected PASSED                  [100%]

================================ 7 passed in 0.15s =================================

////////////////////////

 ~/metody_wir/breach-checker $docker compose exec backend pytest -v
=============================== test session starts ================================
platform linux -- Python 3.12.16, pytest-8.4.2, pluggy-1.7.0 -- /usr/local/bin/python3.12
cachedir: .pytest_cache
rootdir: /app
collected 7 items                                                                  

tests/test_check.py::test_leaked_hash_is_found PASSED                        [ 14%]
tests/test_check.py::test_unknown_hash_is_not_found PASSED                   [ 28%]
tests/test_check.py::test_missing_hash_field PASSED                          [ 42%]
tests/test_check.py::test_plaintext_password_is_rejected PASSED              [ 57%]
tests/test_check.py::test_upper_is_found FAILED                              [ 71%]
tests/test_check.py::test_hash_too_short_rejected PASSED                     [ 85%]
tests/test_check.py::test_hash_invalid_char_rejected PASSED                  [100%]

===================================== FAILURES =====================================
_______________________________ test_upper_is_found ________________________________

client = <FlaskClient <Flask 'app'>>

    def test_upper_is_found(client):
        response = client.post("/check", json={"hash": LEAKED.upper()})
>       assert response.status_code == 200
E       assert 400 == 200
E        +  where 400 = <WrapperTestResponse streamed [400 BAD REQUEST]>.status_code

tests/test_check.py:29: AssertionError
============================= short test summary info ==============================
FAILED tests/test_check.py::test_upper_is_found - assert 400 == 200
=========================== 1 failed, 6 passed in 0.07s ============================

////////////////////////////////

testy z zajec nie wykrywaly usuniecia .lower(), poniewaz skrot byl w obu przypadkac zapisany mlymi znakami, ktore pasowaly do bazy. po dodaniu testu upper is found, skrot byl przesylany jako wielkie znaki ktorych juz nie dalo sie dopasowac do bazy. 

2====================================

 ~/metody_wir/breach-checker $curl -s -o blad.html -w "HTTP %{http_code}\n" -X POST http://localhost:8080/api/check -H "Content-Type: application/json" -d '{"hash":"7c4a8d09ca3762af61e59520943dc26494f8941b"}'
HTTP 500

//////////////////////////////

~/metody_wir/breach-checker $docker compose ps -a
NAME                        IMAGE                     COMMAND                  SERVICE    CREATED          STATUS                     PORTS
breach-checker-backend-1    breach-checker-backend    "flask --app app run…"   backend    42 minutes ago   Exited (1) 2 seconds ago   
breach-checker-db-1         postgres:16-alpine        "docker-entrypoint.s…"   db         42 minutes ago   Exited (0) 7 minutes ago   
breach-checker-frontend-1   breach-checker-frontend   "/docker-entrypoint.…"   frontend   42 minutes ago   Up 42 minutes              0.0.0.0:8080->80/tcp, [::]:8080->80/tcp

~/metody_wir/breach-checker $docker inspect --format '{{.State.Status}} {{.State.ExitCode}}' breach-checker-backend-1
exited 1

///////////////////////////////
                            |wylaczona baza     | blokada     
Status w docker compose ps  |exited (1) po restarcie            |up (healthy)
CPU w docker stats          |okolo 0               |okolo 0
Ostatnie linie logów        |brak polaczenia z hostem db                   |rozpoczete zadanie, brak zakonczenia
Co dostał klient            |blad 500 i strone bledu z tracebackiem                   |blad 504
Po jakim czasie             |pare sekund, po probie polaczenia z baza               |okolo minuty

////////////////////////////////

Blad pokazywal dokladnie sciezki plikow i fragmenty kodu gdzie nastapily bledy. ujawnianie ich uzytkownikom wskazyaloby na konkretne istniejacepodatnosci aplikacji.

3. =====================================

przed - caly plik nginx.conf, 
po:
~/metody_wir/breach-checker $curl http://localhost:8080/nginx.conf
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx/1.31.6</center>
</body>
</html>

/////////////

dlaczego obraz produkcyjny frontendu nie ma tego problemu?

poniewaz w frontend/dockerfile indstrukcja copy kopiuje tylko 3 potrzebne pliki strony do katalogu publicznego

dlaczego montowanie całego katalogu projektu do katalogu serwowanego przez serwer WWW jest ryzykowne.
poniewaz gdy pliki konfiguracji zostana udostepnione przez nginx , struktura i czesc sposobu dzialania aplikacji rowniez zostanie przekazana. 
