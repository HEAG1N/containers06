Crearea unei aplicații multi-containe

Scopul lucrării

Famialiarizarea cu gestiunea aplicației multi-container creat cu docker-compose.

Sarcina

Creearea unei aplicații php pe baza a trei containere: nginx, php-fpm, mariadb, folosind docker-compose.

Execuție

Cream un repozitoriu containers06 și il copiem pe computerul nostru.

Site-ul pe php

În directorul containers06 cream directorul mounts/site. În acest director, rescriem site-ul pe php, creat în cadrul disciplinei php.

![430704726-35df8604-4e71-4c23-ab0a-0d74599929f6](https://github.com/user-attachments/assets/bb4b8489-c039-4279-9b7d-170d2b04adda)

Fișiere de configurare

Cream fișierul .gitignore în rădăcina proiectului și adăugam următoarele linii:

![430704769-f3fd3fa5-05eb-4648-9a5e-dc208b40950c](https://github.com/user-attachments/assets/974fc5e6-1f33-49e0-ad48-d501d6804d5d)


Cream în directorul containers06 fișierul nginx/default.conf cu următorul conținut:

![430704801-f17a7026-de32-49d5-b252-a2040d6a9c99](https://github.com/user-attachments/assets/92351da1-2b2c-4d94-a016-9ab760a6dced)

Cream în directorul containers06 fișierul docker-compose.yml cu următorul conținut:

![430704838-62893042-0564-4cca-b11e-943f261359e8](https://github.com/user-attachments/assets/ccc601d8-db6d-47ee-9576-05229c6e78fc)

Cream fisierul mysql.env în rădăcina proiectului și adăugam în el următoarele linii:

![430706981-8614997a-ccab-4474-bdcd-280f92703de5](https://github.com/user-attachments/assets/06035c7d-22c4-4a1d-8d4f-305b1d9ccc72)

Pornire și testare

Pornim containerele cu comanda:

docker-compose up -d

![430704898-52f20f37-0493-4c8e-960d-4614a9045a07](https://github.com/user-attachments/assets/51c9edf0-295b-48d5-bd3c-157afe0d4fbc)

Verificam funcționarea site-ului în browser, trecând la adresa http://localhost.

![430705049-acdc008c-ede1-42ee-bac5-612f80b8c822](https://github.com/user-attachments/assets/f2bc37a6-6c5e-47f1-aa92-dd2f90bc1b97)

Răspunsuri la întrebările:
În ce ordine sunt pornite containerele?

Containerele sunt pornite conform dependențelor definite în docker-compose. Docker Compose va încerca să le pornească în paralel, dar dacă există dependențe ("depends_on"), va respecta ordinea. În cazul nostru, cum nu avem dependențe explicite, containerele sunt lansate în paralel, dar serviciile care depind de altele vor aștepta până când serviciile dependente sunt disponibile.
Unde sunt stocate datele bazei de date?

Datele bazei de date sunt stocate într-un volum numit db_data, care este definit în secțiunea volumes a fișierului docker-compose.yml. Fizic, volumul este gestionat de Docker și stocat în sistemul de fișiere al gazdei.
Cum se numesc containerele proiectului?

Containerele proiectului vor fi numite automat ca: containers06_frontend_1, containers06_backend_1 și containers06_database_1, unde "containers06" este numele directorului proiectului.
Trebuie să adăugați încă un fișier app.env cu variabila de mediu APP_VERSION pentru serviciile backend și frontend. Cum se face acest lucru?

Cream fișierul app.env:

echo 'APP_VERSION=1.0.0' > app.env

Apoi modificam fișierul docker-compose.yml pentru a adăuga acest fișier la serviciile respective:
```
  frontend:
  image: nginx:1.19
  volumes:
    - ./mounts/site:/var/www/html
    - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
  ports:
    - "80:80"
  networks:
    - internal
  env_file:
    - app.env

backend:
  image: php:7.4-fpm
  volumes:
    - ./mounts/site:/var/www/html
  networks:
    - internal
  env_file:
    - mysql.env
    - app.env
```
Concluzii:
În cadrul acestei lucrări de laborator, am învățat cum să configurăm și să gestionăm o aplicație PHP multi-container folosind Docker Compose. Am configurat trei containere diferite care lucrează împreună pentru a furniza o aplicație web completă: nginx pentru servirea conținutului, PHP-FPM pentru procesarea scripturilor și MySQL pentru baza de date. Docker Compose simplifică semnificativ gestionarea aplicațiilor complexe, făcând posibilă definirea întregii infrastructuri într-un singur fișier de configurare. Acest lucru facilitează dezvoltarea, testarea și implementarea aplicațiilor într-un mod consistent și reproducibil.
