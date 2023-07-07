# M300 LB2 - Container Service

Eine Projektarbeit von:

Matteo Causin 
Informatiker EFZ Systemtechnik
UBS AG 
3. Lehrjahr 



## Inhalt
- [M300 LB2 - Container Service](#m300-lb2---container-service)
  - [Inhalt](#inhalt)
  - [Einführung](#einführung)
  - [Dockerfile](#dockerfile)
  - [Docker-Compose](#docker-compose)
  - [Starten](#starten)
  - [Beenden](#beenden)
  - [Testing](#testing)
   
<a name="Einführung"></a>
## Einführung

Ziel war es einen Service in Docker zu installieren, welcher auf jedem Rechner Wiederholbar und konsistent ausführbar ist. 
In diesem Beispiel handelt es sich um einen Apache Webserver. Der Service startet lediglich mit einem Befehl. Das Image wird mit Hilfe eines Dockerfiles erstellt. Andere Konfigurationen sind im docker-compose.yml File festgehalten worden. Sobald dieser läuft ist es möglich die Website aufzurufen. 

![Ports](https://github.com/maathumitha/M300/blob/main/img/Ports.png)

<a name="Dockerfile"></a>
## Dockerfile
Mit dem Dockerfile wird das Image erstellt, welches dann für den Container verwendet werden kann.
```
#Baseimage
FROM httpd:latest

#Website-Files wie bspw. index.html
COPY ./website/ /usr/local/apache2/htdocs/

#Port exposing
EXPOSE 80

```
<a name="Docker-Compose"></a>

## Docker-Compose
Im Docker-Compose.yml FIle werden Konfigurationen am Image festgehalten. Wichtig ist, dass der Pfad des Dockerfiles auch angegeben wird. Auch sind Netzwerkkonfigurationen für die Ports in diesem File konfiguriert worden. 
Achtung: Die Syntax beim .yml File ist sehr wichtig und kann schnell zu Fehlern führen.
[Quelle](https://www.theserverside.com/blog/Coffee-Talk-Java-News-Stories-and-Opinions/Simple-Apache-docker-compose-example-with-Dockers-httpd-image)
```
version: '3'
services:
  web:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - 8082:80
    restart: always 
```



<a name="Starten"></a>
## Starten
Mit folgendem Befehl, kann der Container gestartet werden. Nach dem der Container das erste Mal gestartet worden ist, kann der Parameter "--build" weggelassen werden.
```
docker-compose up --build -d
```

<a name="Beenden"></a>
## Beenden
Mit folgendem Befehl, kann der Container beendet werden.
```
docker-compose down
```

<a name="Testing"></a>
## Testing
Sobald der Container gestartet worden ist kann über <http:\\127.0.0.1:8082> bzw. http:\\localhost:8082 die Website aufgerufen werden. 
Es wird der Inhalt des Dokuments im Ordner "./website/"  aus dem Dockerfiles angezeigt. 

![Access_test](https://github.com/matteocsn/M300/blob/main/img/Website-LB2.png)
