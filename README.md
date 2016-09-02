# docker-12c-ee-dbca

Ein fertiges Image gibt es auf https://hub.docker.com/r/andybpunkt/oracle-12/

Pull Request mit Docker: docker pull andybpunkt/oracle-12

In Ordner Step1 müssen die Oracle Database 12c EE Installationdateien 
linuxamd64_12102_database_1of2.zip und linuxamd64_12102_database_2of2.zip für Linux gespeichert werden:

http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html

Befehle nacheinander in der Console eingeben (z.B. ConEmu ist sehr angenehm zu bedienen):

- cd /Ordnerpfad

- docker build -t oracle-12c:step1 step1

- docker run --shm-size=4g -ti --name step1 oracle-12c:step1 /bin/bash

- /tmp/install/install

Warten bis Successfully Setup Software kommt! Dauert einige Minuten.

- <enter>-drücken

- exit

- docker commit step1 oracle-12c:installed

- docker build -t oracle-12c:step2 step2

- docker run --shm-size=4g -ti --name step2 oracle-12c:step2 /bin/bash

- /tmp/create

- su - oracle 

- cd /tmp/

- dbca -silent -createDatabase -templateName General_Purpose.dbc  -gdbname ORCL -sid ORCL -responseFile ./db_install.rsp  -characterSet AL32UTF8  -sysPassword admin  -systemPassword admin  -createAsContainerDatabase true  -numberOfPDBs 1  -pdbName pdb_tacodb  -pdbAdminPassword admin  -databaseType MULTIPURPOSE  -automaticMemoryManagement false  -storageType FS  -ignorePreReqs

Warten! Dauert einige Minuten.

- 2x exit

- docker commit step2 oracle-12c:created

- docker build -t oracle-12c step3
---------------------------------------------------------------------------
Image wurde erstellt! Glückwunsch

Als nächstes Datenbank starten:

- docker run --shm-size=4g -p 1521:1521 -ti --name fresh_db oracle-12c:latest /bin/bash

- /tmp/start


Um eine neue Shell auf dem Container aufzubauen:

- docker exec -it fresh_db bash

'docker images' gibt alle erstellten images zurück. Sie bauen alle aufeinander auf. 
Man kann aber auch ein neues Image aus einem Zwischenschritt erzeugen, falls man Änderungen einfügen möchte. 

'docker ps -a' gibt alle erzeugen Container zurück. Mit 'docker start "container ID oder Tag"' startet der container.
