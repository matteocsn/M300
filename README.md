# M300 LB2 - Container Service

Eine Projektarbeit von:

Matteo Gioachino Causin </br>
Informatiker EFZ Systemtechnik </br>
3. Lehrjahr </br>
UBS AG </br>


## Inhalt
- [M300 LB2 - Container Service](#m300-lb2---container-service)
  - [Inhalt](#inhalt)
  - [Einführung](#einführung)
  - [Vorbereitung mit Vagrant und Bitvise](#vorbereitung-mit-vagrant-und-bitvise)
  - [Dockerfile](#dockerfile)
  - [Docker-Compose](#docker-compose)
  - [Starten](#starten)
  - [Beenden](#beenden)
  - [Testing](#testing)
  - [Quellen](#quellen)
   

<a name="Einführung"></a>
## Einführung

Ziel war es einen Service in Docker zu installieren, welcher auf jedem Rechner Wiederholbar und konsistent ausführbar ist. 
In diesem Beispiel handelt es sich um einen Apache Webserver. Der Service startet lediglich mit einem Befehl. Das Image wird mit Hilfe eines Dockerfiles erstellt. Andere Konfigurationen sind im docker-compose.yml File festgehalten worden. Sobald dieser läuft ist es möglich die Website aufzurufen. 

<a name="Vorbereitung"></a>
## Vorbereitung mit Vagrant und Bitvise 

Für diese Projekt erstellen wir eine Vagrant VM auf der wir dann das Projekt ausführen. Damit diese fuktioniert brauchen wir ein Vagrant File. Dies geht mit folgendem befehl 


```vagrant init```


In diesem File ist definiert wie die Vm aussehen soll: 

```
# -*- mode: ruby -*-
# vi: set ft=ruby :

#
#	Ubuntu Jammy 64-bit Linux mit Docker
#

Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/jammy64"

  # Create forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. 
  # NOTE: This will enable public access to the opened ports
  config.vm.network "forwarded_port", guest:8082, host:8082, auto_correct: true
    
  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.hostname = "docker"
  # Share an additional folder to the guest VM.
  config.vm.synced_folder ".", "/mnt"

  config.vm.provider "virtualbox" do |vb|
     vb.memory = "2048"
  end
  
  config.vm.provision "shell", inline: <<-SHELL
  #Get Docker Image
  sudo apt-get update
  sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
	
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg 
  echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  
  #Install Docker
  sudo apt-get update
  sudo apt-get install docker-ce docker-ce-cli containerd.io -y
  sudo curl -L "https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
  sudo chmod +x /usr/local/bin/docker-compose

  SHELL

  # add ssh public keys for root and vagrant 
  config.vm.provision "shell", path: "scripts/add_ssh_pub.sh"
  # add alias
  config.vm.provision "shell", path: "scripts/add_alias.sh"
  
end
```

Hier drin ist auch definiert welches Betriebssystem verwendet wird. 

mit ``` vagrant up ``` kann man die Vm dann aufbauen und starten

Mit Bitvise erstelle ich dann einen SSH key der in meinem lokalen vagrant ordner erstellt wird. Danach kann ich mich mit dem treminal auf die VM verbinden. 

Wenn dies vollbracht ist, geht es wie folgt weiter: 


<a name="Dockerfile"></a>
## Dockerfile
Mit dem Dockerfile wird das Image erstellt, welches dann für den Container verwendet werden kann.
```
#Baseimage
FROM nginx:alpine

#der "." definiert das alle existing files in diese repository gezogen werden. im letzten Ordner "html" wird dann das index.hmtl File gemountet. 
COPY . /usr/share/nginx/html

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
  nginx:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - 8082:80
    volumes:
      - ./site-content:/usr/share/nginx/html
```



<a name="Starten"></a>
## Starten
Mit folgendem Befehl, kann der Container gestartet werden. Nach dem der Container das erste Mal gestartet worden ist, kann der Parameter "--build" weggelassen werden.
```
docker-compose up --build -d
```

<a name="Beenden"></a>
## Beenden
Mit folgendem Befehl, wird der ontainer heruntergefahren
```
docker-compose down
```

<a name="Testing"></a>
## Testing
Sobald der Container gestartet worden ist kann über <http:\\127.0.0.1:8082> bzw. <http:\\localhost:8082> die Website aufgerufen werden. 
Es wird der Inhalt des Dokuments im Ordner "./site-content/"  aus dem Dockerfiles angezeigt. 

![Access_test](https://github.com/matteocsn/M300/blob/main/img/Website-LB2.png)


## Quellen

- <https://www.theserverside.com/blog/Coffee-Talk-Java-News-Stories-and-Opinions/Simple-Apache-docker-compose-example-with-Dockers-httpd-image>