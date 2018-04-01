# Administration des services UL



















## proasul1

### Comptes

    hosts : proasul1.prodinfo.fr.cly / subversion-webdev.ib.fr.cly

Pour changer de mot de passe :

    bash-3.2$ passwd

## proasul2

### Comptes

    hosts : proasul2.prodinfo.fr.cly 


### Apache

Le serveur Apache est installé à l'emplacement :

   /tools/list/product/apache-2.2.9


#### Configuration

Le fichier de configuration du serveur se trouve à l'emplacement

   /app/list/apache/conf.d/ic.conf

#### Arrêt / Relance

Sous un login autorisé (user dans groupe MID):

sudo /sbin/service apache-http-jenkins start | stop

*En cas de difficulté, utiliser l'ancienne méthode. Celle-ci sera définitivement supprimée lorsque le lancement par service sera validé.*


//Se connecter en maven et aller sur : 

   /app/list/data/tools/exploit/ic/

startAPACHE.sh ou stopAPACHE.sh//


### Safe restart Jenkins

http://jenkins-webdev.ib.fr.cly/jenkins/safeRestart

### Tomcat

Le serveur Tomcat est installé à l'emplacement :
   /app/list/ic/tomcat7_jenkins



#### Arrêt / Relance

 Sous un login autorisé (user dans groupe maven):

sudo /sbin/service apache-tomcat-jenkins start | stop 

*En cas de difficulté, utiliser l'ancienne méthode. Celle-ci sera définitivement supprimée lorsque le lancement par service sera validé. *

//Les scripts de démarrage et d'arrêt se trouvent dans :
   /app/list/data/tools/exploit/ic//


En cas de problèmes contacter :

*   Equipe Midlleware chez SILCA 

### Espace disque

`<note tip>`
Si le filesystem /app/list/ic/hudson arrive à saturation, un script (à exécuter avec le user maven) permet de vider les jdk du dossier Tomcat pour chaque job concerné (NOM_DU_JOB/workspace/NOM_DU_PROJET/target/Tomcat/jdk1.6.0/) :

	
	           sudo su - maven
	           cd /app/list/ic/hudson/jobs/
	           ./deleteTomcatJDK.sh

`</note>`
`<note>`
Temps indicatif d'exécution : 1h30
`</note>`
`<note warning>`
Ce script modifie les workspace donc il ne faut le lancer que lorsqu'aucun build n'est en cours (i.e. entre 12h et 14h ou la nuit)
`</note>`


#### Vider la liste d'attente des builds

Aller sur l'url : http://hudson-webdev.ib.fr.cly/hudson/script puis copier/coller le bout de code suivant :

	
	import hudson.model.*
	def queue = Hudson.instance.queue
	println "Queue contains ${queue.items.length} items"
	queue.clear()
	println "Queue cleared"


Puis exécutez et voilà !


## [Futur] Subversion

`<note warning>`N'est pas encore en production`</note>`

### Info de Connection

`<note tip>`Connexion SSH user appadm01 sur 10.56.137.15`</note>`



### Serveur HTTP

[http://subversion-test-webdev.ib.fr.cly/pp](http://subversion-test-webdev.ib.fr.cly/pp)

### TODO ###

Remplacer par subversion-webdev.ib.fr.cly quand ce sera migré

### Build de l'image

	
	cd /home/appadm01/docker/docker-subversion
	docker build -t ul/subversion-lcl .


### Démarrage

	
	docker run --add-host lclhp-svn:10.56.137.15 --add-host lp002app0019.prodinfo.gca:10.179.225.27 --add-host id.fr.cly:10.242.16.132 -d --cpuset-cpus="4" --memory="12g" -p 80:80 -p 443:443 -p 32770:22 -v /app/list/data/svn/conf:/svn/conf  -v /app/list/data/svn/repos:/svn/repos -v /app/list/data/svn/reposdump:/svn/reposdump -v /app/list/data/svn/logs:/var/log/apache2 -v /app/list/data/svn/livraisons:/livraisons --name subversion-srv ul/subversion-lcl

### Arrêt

	
	docker stop subversion-srv
	docker rm subversion-srv

### Entrer dans le conteneur

	
	docker exec -it subversion-srv bash


### Logs du serveur

	
	docker logs -f subversion-srv

### Sauvegardes

############################ TODO ############################

### Hooks

Les hooks sont situés à l'extérieur du conteneur dans :
 | Repo SVN | Chemin                                  | Usage                               | 
 | -------- | ------                                  | -----                               | 
 | pp       | /app/list/data/svn/repos/pp/hooks       | empêcher les modifications de tags | 
 | ul       | /app/list/data/svn/repos/ul/hooks       | empêcher les modifications de tags | 
 | hphj2ee  | /app/list/data/svn/repos/hphj2ee /hooks | Workflow de livraison hors Planet   | 
## proassdis1f2 [OLD]

### Comptes

    hosts : proassdis1f2a.prodinfo.fr.cly

 | Login | Mot de passe | Utilisation                        | 
 | ----- | ------------ | -----------                        | 
 | maven | kcroot       | compte pour l'administration maven | 


### Apache

Connexion en svnadmin

Configuration
    /etc/httpd/conf.d

Démarrage : 
    sudo /sbin/service httpd start
Arrêt : 
    sudo /sbin/service httpd stop
Redémarrage : 
    sudo /sbin/service httpd restart

### Tomcat

#### Administration
Lancer la console d’administration Tomcat
(login : tomcat)

Pour arreter/démarrer tomcat, utiliser les commandes dans le répertoire /app/list/sites/tomcat/bin :



### Administration de l'Intranet Qualité

`<note important>` ATTENTION: Ce n'est pas la manip à faire mensuellement pour fournir les infos à Catherine`</note>`

Pour lancer le batch de récupération des métrics F9/F6 en temps réel :

    1. sur le serveur proassdis1f2 en user maven, copier le fichier QUALITY.properties dans WEB-INF/classes/
    cd /app/list/sites/tomcat/webapps/quality/WEB-INF/applications/QUALITY
    cp Properties/QUALITY.properties ../../classes/
    
    2. Dans un navigateur, lancer UNE SEULE FOIS l'url :
 
    http://quality-webdev.ib.fr.cly/quality/outil/quality/Batch/metrics

    3.  pour suivre l'exécution des logs :
    [maven@proassdis1f2 QUALITY]$ tail -f /app/list/sites/tomcat/logs/catalina.out

### Archiva

`<note warning>`

**User maven**
`</note>`

Check si le processus est bien lancé
     ps -ef |grep -i archiva

Stop 
     su - mvnusers -c "/usr/local/apache-archiva/bin/archiva stop"
     (password = mvnusers)
Start
     su - mvnusers -c "/usr/local/apache-archiva/bin/archiva start"
     (password = mvnusers)

Paramétrage Jetty (paramétrage mémoire JVM du jetty en particulier)
     /usr/local/apache-archiva/conf/wrapper.conf

## LD002MYS5803 (VM Intranet)

Adresse IP :
    10.179.139.66

Outils hébergés :
    Redmine (3 instances) + base de données postgresql associée
    Console ADM
    OpenId
    Deployment_launcher (déploiements sur Red Hat)
    ule_services (2 instances)
    Testeur ADSU APPNAT





### Postgresql (Base Redmine)

`<note warning>`

**User appadm01**
`</note>`

Démarré au boot : **oui**

Démarrage :
    sudo service postgresql start

Arrêt :
    sudo service postgresql stop

Sauvegarde (chaque vendredi 19h) :
    /app/list/data/dump_db_redmine/ 

### Redmine

`<note warning>`

**User appadm01** (Surtout pas en root !)
`</note>`

Démarré au boot : **oui**

Démarrage global (toutes les instances)
    sudo service redmine start

Démarrage d'une instance correspondant à un port `<//pppp//>`:
    sudo service redmine_`<pppp>` start

Arrêt global (toutes les instances)
    sudo service redmine stop

Arrêt d'une instance correspondant à un port `<//pppp//>`:
    sudo service redmine_`<pppp>` stop

Redémarrage global (toutes les instances)
    sudo service redmine restart

Redémarrage d'une instance correspondant à un port `<//pppp//>`:
    sudo service redmine_`<pppp>` restart

Répertoire d'installation de Redmine
    /app/list/software/redmine/

Chemin des pièces jointes
    /app/list/software/redmine/files

Ports de fonctionnement :
    3000, 3001, 3002

### Apache HTTP Server

`<note warning>`

**User appadm01**
`</note>`

Démarré au boot : **oui**

Démarrage : 
    sudo service apache-httpd start

Arrêt :
    sudo service apache-httpd stop

Restart
    sudo service apache-httpd restart

Logs
    /app/list/l002mys5803d1/apache_httpd/httpd/logs

Config
    /app/list/l002mys5803d1/apache_httpd/httpd/conf/httpd.conf

Paramétrage / création password carto
    Activer mod_auth_digest.so
    mkdir ./.secure
    ./bin/htdigest -c ./.secure/.passwords-carto "Administration Cartographie" admin
    >>> Entrer le mot de passe

Port de fonctionnement :
    80 & 443

Renouvellement Certificat SSL / commandes openssl :
    openssl pkcs12 -in intranet-webdev.ib.fr.cly.p12 -out intranet-webdev.ib.fr.cly.pem  -nokeys -nodes  
    openssl pkcs12 -in intranet-webdev.ib.fr.cly.p12 -out intranet-webdev.ib.fr.cly.key -nocerts -nodes
    

### Tomcat 7 intranet

`<note warning>`User appadm01** (Surtout pas en root !)`</note>`

Démarré au boot : **oui**

Démarrage : 
    sudo service tomcat7_intranets start

Arrêt :
    sudo service tomcat7_intranets stop

Logs :
    /app/list/l002mys5803d1/tomcat7_intranets/logs

Port de fonctionnement :
    8181



### ULE Services


[http://git-webdev.ib.fr.cly:7000/docs/ul%2Fuleservices.git](http://git-webdev.ib.fr.cly:7000/docs/ul%2Fuleservices.git)

### Job Scheduler

\\
Arrêt :

	
	/app/list/l002mys5803d1/jobscheduler/stop.sh


Démarrage :

	
	/app/list/l002mys5803d1/jobscheduler/start.sh


Re-démarrage :

	
	/app/list/l002mys5803d1/jobscheduler/restart.sh


Logs :

	
	tail -f /app/list/l002mys5803d1/jobscheduler/jobscheduler.log



### Board Scheduler

Effectue principalement 

*   Appel aux urls de monitorings sur l'ensemble des machines ( MONITORING : délai 1m, FS : délai 1H ,  MEM : délai 1H, IP : délai 24h)
*   Conversion des données de la collections MONITORINGS vers la collection BOARD 
*   Calcul des stats sur une période de 1 mois depuis la collection BOARD, le résultat est dans la collection BOARD_STATS.
##### Git

[http://git-webdev.ib.fr.cly:7000/summary/ul%2Fboardscheduler.git](http://git-webdev.ib.fr.cly:7000/summary/ul%2Fboardscheduler.git)
\\
#####  Arrêt:== 

`/app/list/l002mys5803d1/boardscheduler/stop.sh `

#####  Démarrage:== 

	
	/app/list/l002mys5803d1/boardscheduler/start.sh


#####  Re-démarrage:== 

	
	/app/list/l002mys5803d1/boardscheduler/restart.sh


#####  Logs:== 

	
	tail -f /app/list/l002mys5803d1/boardscheduler/boardscheduler.log


### Montage NFS

#####  Re-démarrage :

	
	sudo su - root
	service nfs start



## lp002app0017 (wiki, slaves docker, mongodb)

`<note warning>`

**User appadm01**  (Production)
`</note>`

Se connecter en 2 temps :
 1.  Adresse ip : 10.179.225.25
 2.  Connexion avec votre compte personnel de __production__
 3.  sudo su - appadm01

Outils hébergés
 1.  Wiki
 2.  Slaves Docker
 3.  Base MongoDB (ReplicaSet)
### Docker Engine

#### Démarrage
    sudo systemctl start docker

#### Redémarrage

    sudo systemctl restart docker


Remarque : sur la machine lp002app7987 (Nexus), il faut utiliser :
    sudo service docker start|stop|restart

### MongoDB (ReplicaSet)

#### Démarrage

#Noeud 1 (lp002app0017.prodinfo.gca / bdul1.devinfo.fr.cly)
    /home/appadm01/mongodb-ul/start-mongo-1.sh

#Noeud 2 (lp002app0018.prodinfo.gca / bdul2.devinfo.fr.cly)

    /home/appadm01/mongodb-ul/startMongo-2.sh

#Noeud 3 (lp002app0019.prodinfo.gca / bdul3.devinfo.fr.cly)

    /home/appadm01/mongodb-ul/startMongo-3.sh    

#### Arrêt

#Noeud 1 (lp002app0017.prodinfo.gca / bdul1.devinfo.fr.cly)
    /home/appadm01/mongodb-ul/stopMongo-1.sh

#Noeud 2 (lp002app0018.prodinfo.gca / bdul2.devinfo.fr.cly)

    /home/appadm01/mongodb-ul/stopMongo-2.sh

#Noeud 3 (lp002app0019.prodinfo.gca / bdul3.devinfo.fr.cly)

    /home/appadm01/mongodb-ul/stopMongo-3.sh    

#### Console mongodb

#Noeud 1 (lp002app0017.prodinfo.gca / bdul1.devinfo.fr.cly)

    docker exec -it mongo-ul-01 mongo admin

#Noeud 2 (lp002app0018.prodinfo.gca / bdul2.devinfo.fr.cly)

    docker exec -it mongo-ul-02 mongo admin

#Noeud 3 (lp002app0019.prodinfo.gca / bdul3.devinfo.fr.cly)

    docker exec -it mongo-ul-03 mongo admin

####  Commandes admin MongoDB

#Choix base de données

    use XXXXX

#Authentification sur la base active

    db.auth("root", "password");

#Status du replicaset

    rs.status()
    
#Création user yyyyy avec mot de passe wwwwww sur la base XXXXX 

    use XXXXX 
    db.createUser({user : "yyyyy",
        pwd:"wwwwww",
        roles: [ { role: "readWrite", db: "XXXXX " } ]
      })

#### Logs

Les logs sont disponibles, sur chacune des machines à l'endroit suivant :
    /app/list/data/mongo-logs/mongodb.log

Concernant la gestion des logs, la rotation n'est pas automatique. Un job a été créé pour forcer la rotation de chacun des membres du replicat set.
Ce qui veut dire que si on ajoute un membre un jour, il faudra l'ajouter à ce job :

   http://jenkins-webdev.ib.fr.cly/jenkins/user/admin/my-views/view/Cron%20Usine%20Logicielle/job/Cron_mongo_log_management/

#### En cas de crash

`<note important>`Agir le plus vite possible après la notification de crash, cela augmente les chances que la resynchro se passe bien et donc écarte le risque de perdre des données`</note>`

`<note>`
    USER appadm01
    Fichiers data dans /app/list/data/ulprod
    Fichiers logs dans /app/list/data/mongo-logs
    Scripts de démarrage dans /home/appadm01/mongodb-ul/
`</note>`

Commencer par repérer les primaires, secondaires et serveurs non connectés avec mongochef.
{{ :private:mongochef.png?nolink |}}

Deux cas :
    1. Il reste un primaire
    2. Il ne reste pas de primaire.

##### Il reste un primaire

 1.  Si besoin arrêter les conteneurs non connectés (docker stop XXX & docker rm XXX)
 2.  Relancer les conteneurs (/home/appadm01/mongodb-ul/startMongo-X.sh)
 3.  Attendre quelques dizaines de minutes pour voir si la resynchro se fait.
 4.  Voir les logs
 5.  Si elle ne se fait pas et que l'on voit dans les logs des erreurs indiquant qu'on est trop desynchronisé :
     - arrêter le conteneur
     - supprimer le répertoire data (faire un mv vers un autre nom)
     - relancer le conteneur
     - la resynchro prend entre 1h et 2h et fait ramer toute la base...
     - si deux noeuds sont HS, les traiter l'un après l'autre.

#####  Il ne reste pas de primaire

 1.  repérer le dernier noeud en fonctionnement (probabilité forte pour que ce soit le dernier primaire)
    - ls -ltr /app/list/data/ulprod   sur les trois machines
    - celui avec des fichiers les plus récents est le dernier primaire (probablement).
 2.  relancer le conteneur
 3.  attendre qu'il soit indiqué comme primaire dans mongochef
 4.  appliquer la todolist précédente (il reste un primaire).

#### Sauvegarde

La sauvegarde est exécuté par le job jenkins
    http://jenkins-webdev.ib.fr.cly/jenkins/user/admin/my-views/view/Cron%20Usine%20Logicielle/job/Cron_mongo_save_database/
Le résultat arrive sur la machine lp002app0017.prodinfo.gca dans le répertoire
    /app/list/data/save

### Wiki Dev Java Web

#### Dockerfile

`/app/list/data/wiki`

#### Contenu du wiki

`/app/list/data/wiki/wiki`

### Build de l’image

`docker build -t wip/wiki-dev .`

#### Démarrage du conteneur

`docker run -d -p 80:80 -p 443:443  -v /app/list/data/wiki/wiki:/var/www/html  --name wikidev wip/wiki-dev`

#### Entrer dans le conteneur

`docker exec -it wikidev bash`

#### Voir les conteneurs actifs

`docker ps -a`

#### Arrêter un conteneur

`docker stop wikidev`

#### Supprimer un conteneur

`docker rm wikidev`

### Renouvellement du certificat TLS

Le nouveau certificat est à copier dans
`/app/list/data/wiki/wiki-webdev.ib.fr.cly.pem`
la nouvelle clé privée est à copier dans 
`/app/list/data/wiki/wiki-webdev.ib.fr.cly.key`
Ensuite il faut rebuilder l'image et relancer le conteneur.


### Crons

Cron sur la machine :
`<code>`clean docker containers

*/60 * * * *  /home/appadm01/docker/ule-ha/clean.sh >/tmp/clean.log 2>/tmp/clean.err

#On clean les fichier de logs générés par mongo (trigger par http://jenkins-webdev.ib.fr.cly/jenkins/job/Cron_mongo_log_management)

45 22 * * * find /app/list/data/mongo-logs/* -type f -mtime +10 | xargs sudo rm -rf`</code>`
## lp002app0018 (slaves docker, mongodb, ansible)

`<note warning>`

**User appadm01**  (Production)
`</note>`

Se connecter en 2 temps :
 1.  Adresse ip : 10.179.225.26
 2.  Connexion avec votre compte personnel de __production__
 3.  sudo su - appadm01

Outils hébergés
 1.  Slaves Docker
 2.  Base MongoDB (ReplicaSet)
 3.  Conteneur Ansible
 4.  Phabricator (run + DB)

### MongoDB (ReplicaSet)

[voir machine lp002app0017](private/admin_services_ul#mongodb_replicaset)

### Ansible

`<note warning>`
Pour l'instant il est nécessaire d’exécuter les playbooks depuis la machine lp002app0018 car les accès ssh par clé rsa ne sont pas autorisés (demande SILCA en cours)
`</note>`


#### Projet GIT

[http://git-webdev.ib.fr.cly:7000/summary/ul/scripts/ansible](http://git-webdev.ib.fr.cly:7000/summary/ul%2Fscripts%2Fansible.git)

#### Build image docker

    docker build -t ul/ansible .

#### Exécuter un playbook

    docker run -it --name ansible --rm ul/ansible ansible-playbook /playbooks/deploy-scripts/deploy-scripts-rp.yml -f 10
    
####  Ajouter une machine

 1.  Ajouter la clé publique du serveur (etc/ssh/ssh_host_rsa_key.pub) dans .ssh/known_hosts
 2.  Ajouter la clé publique d'Ansible (.ssh/id_rsa.pub) dans authorized_keys sur la machine "remote"
 3.  Ajouter la machine (DNS) dans le bon groupe dans le fichier ansible-hosts
 4.  Reconstruire l'image

#### Modifier / ajouter un playbook

 1.  Ajouter le playbook dans le répertoire playbooks
 2.  Reconstruire l'image
    
### Crons

Cron sur la machine :
`<code>`clean docker containers

*/60 * * * *  /home/appadm01/docker/ule-ha/clean.sh >/tmp/clean.log 2>/tmp/clean.err

#On clean les fichier de logs générés par mongo (trigger par http://jenkins-webdev.ib.fr.cly/jenkins/job/Cron_mongo_log_management)

45 22 * * * find /app/list/data/mongo-logs/* -type f -mtime +10 | xargs sudo rm -rf`</code>`
## lp002app0019 (slaves docker, mongodb)

`<note warning>`

**User appadm01**  (Production)
`</note>`

Se connecter en 2 temps :
 1.  Adresse ip : 10.179.225.27
 2.  Connexion avec votre compte personnel de __production__
 3.  sudo su - appadm01

Outils hébergés
 1.  Slaves Docker
 2.  Base MongoDB (ReplicaSet)

### MongoDB (ReplicaSet)

[voir machine lp002app0017](private/admin_services_ul#mongodb_replicaset)
### Crons

Cron sur la machine :
`<code>`clean docker containers

*/60 * * * *  /home/appadm01/docker/ule-ha/clean.sh >/tmp/clean.log 2>/tmp/clean.err

#On clean les fichier de logs générés par mongo (trigger par http://jenkins-webdev.ib.fr.cly/jenkins/job/Cron_mongo_log_management)

45 22 * * * find /app/list/data/mongo-logs/* -type f -mtime +10 | xargs sudo rm -rf`</code>`
## lp002mid7750 (Gitblit & Tinyproxy)

Tous les services sont administrés avec l'utilisateur ''mid''.

Se connecter en 2 temps :
 1.  Connexion avec votre compte personnel
 2.  sudo su - mid

### Gitblit

Démarrage (à lancer depuis le répertoire /app/list/gitblit/):

	
	/app/list/gitblit/start.sh



Si vous avez définit les variables d'environnement HTTP_PROXY, HTTPS_PROXY, vous devrez également ajouter la variable d'environnement NO_PROXY, avec la valeur suivante :
    10.179.139.79,10.179.137.99,10.179.137.111

### Tinyproxy

Service : 

	
	/home/mid/tinyproxy_secret/usr/sbin/tinyproxy -c /home/mid/tinyproxy_secret/etc/tinyproxy.conf
	/home/mid/tinyproxy/usr/sbin/tinyproxy -c /home/mid/tinyproxy/etc/tinyproxy.conf



Liste des domaines autorisés :

	
	vim /home/mid/tinyproxy/etc/filter
	vim /home/mid/tinyproxy_secret/etc/filter
	# il faut redémarrer tinyproxy à chaque modif


En cas de problème, vérifier l'espace disque. Si besoin, supprimer les fichiers de logs : 

	
	rm /home/mid/tinyproxy/tinyproxy.log /home/mid/tinyproxy_secret/tinyproxy.log


Pour killer proprement le proxy (avec tous les processus fils)

	
	kill -15 {PID "père"}
	
	ou 
	
	pkill tiny
	


## lp002app7987 (VM Nexus)

Tous les services sont administrés avec l'utilisateur ''mid''.

Se connecter en 2 temps :
 1.  Connexion avec votre compte personnel
 2.  sudo su - mid

Les scripts d'arrêt, démarrage des services se trouvent dans ''/home/mid/bin''.

Les services sont automatiquement démarrés au reboot de la VM avec cron (crontab -l).

### Sonatype Nexus

Nexus permet d'héberger des artefacts Maven venant du LCL et de l'exterieur. Il également utilisé comment référentiel privé NPM.


url : [http://binaries-webdev.ib.fr.cly/nexus](http://binaries-webdev.ib.fr.cly/nexus)

Scripts :

*  status: ''/home/mid/bin/nexus status''

*  stop: ''/home/mid/bin/nexus stop''

*  start: ''/home/mid/bin/nexus start''


Répertoire d'installation : ''/tools/list/product/nexus''

Répertoire de travail (contient notamment les référentiels Maven) : ''/app/list/data/sonatype-work/''

#### Maven

Configuration Maven pour utiliser Nexus à la place des répos par défaut (fichier ~/.m2/settings.xml) :
    <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                        http://maven.apache.org/xsd/settings-1.0.0.xsd">
    `<localRepository/>`
    `<interactiveMode/>`
    `<usePluginRegistry/>`
    `<offline/>`
    `<pluginGroups/>`
    `<servers/>`
    `<mirrors>`
      `<mirror>`
        `<id>`nexus-public`</id>`
        `<url>`http://binaries-webdev.ib.fr.cly/nexus/content/groups/public`</url>`
        `<mirrorOf>``</mirrorOf>`
      `</mirror>`
    `</mirrors>`
    `<profiles/>`
    `<activeProfiles/>`
    `</settings>`

#### Npm

Configuration npm pour utiliser le référentiel nexus :
    npm config set registry http://binaries-webdev.ib.fr.cly/nexus/content/repositories/npm-registry/

Si vous avez définit les variables d'environnement HTTP_PROXY, HTTPS_PROXY, vous devrez également ajouter la variable d'environnement NO_PROXY, avec la valeur suivante :
    10.179.137.99,binaries-webdev.ib.fr.cly

#### Bower

Nexus ne supporte pas encore le format Bower : https://issues.sonatype.org/browse/NEXUS-6884

### Mongo

url : 10.179.137.111:27017

Scripts:

*  start : ''/home/mid/bin/mongod-start''

*  stop : ''/home/mid/bin/mongod-stop''

Répertoire d'installation : ''/tools/list/product/mongodb''

Répertoire des données : ''/app/list/data/mongodb/''

###  Docker Prod

Version installée: Docker version 1.6.1

`<note warning>`user mid** : login avec user nominatif de production + sudo su - mid`</note>`

Utiliser le client Docker
    sudo docker version

Arrêter le service docker :
    sudo service docker stop

Démarrer le service docker :
    sudo service docker start

Redémarrer le service docker :
    sudo service docker restart

Download des images docker [Exemples]
    sudo docker pull yaronr/xwiki-tomcat7
    sudo docker pull mysql

Démarrage du container mysql [Exemple]
    sudo docker run --name xwiki-mysql -e MYSQL_ROOT_PASSWORD=password -d mysql:latest -p 3306:3306

Entrer dans le container mysql [Exemple]
    sudo docker exec -it xwiki-mysql bash

Supprimer un container [Exemple]
    sudo docker rm xwiki-mysql

En cas de bug "no such host" ou "too many open file"
    Arrêter le service docker : sudo service docker stop
    Changer les droits du répertoire docker : sudo chmod -R 777 /app/list/data/docker
    Supprimer le répertoire docker : rm -rf /app/list/data/docker/*
    Démarrer le service : sudo service docker start


## Jobs Jenkins

### IQ : Metrics Services

5 jobs tournent pour alimenter les métriques de l'Intranet Qualité, visibles sur Jenkins dans la vue Cron Usine Logicielle :

**- 1 job générique Metrics_Services qui attend un argument ROOTING qui vaut entre 1 et 4.** 

*  Vaut 1 pour le lancement quotidien de jobs

*  Vaut 2 pour le lancement mensuel de jobs

*  Vaut 3 pour l'enregistrement quotidien des métriques

*  Vaut 4 pour l'enregistrement mensuel des métriques
Ce job exécute le jar Metrics_Services (voir job pour emplacement) avec l'argument ROOTING en paramètre

**- 4 jobs cronés :** 

*  Metrics_services_cron_job_quotidien qui passe du lundi au vendredi à 21h. Ce job appelle le job générique Metrics_Services en lui fournissant ROOTING = 1

*  Metrics_services_cron_job_mensuel qui passe le premier de chaque mois. Ce job appelle le job générique Metrics_Services en lui fournissant ROOTING = 2

*  Metrics_services_cron_enregistrement_quotidien qui passe du mardi au samedi à 8h. Ce job appelle le job générique Metrics_Services en lui fournissant ROOTING = 3

*  Metrics_services_cron_enregistrement_mensuel qui passe tous les dimanches à 20h. Ce job appelle le job générique Metrics_Services en lui fournissant ROOTING = 4


## Ld002MYS6883 (machine de développement Usine Logicielle)

Adresse IP :
    10.179.139.79

Outils hébergés :
    ULE_SERVICES (dev)
    HOLYWORKSPACE (dev)
    JENKINS de test 


### Jenkins de test

`<note tip>`user appadm01   (connexion milicer + sudo su - appadm01)`</note>`

Url
[ http://10.179.139.79:8181/jenkins/](http://10.179.139.79:8181/jenkins/ )

Démarrage du tomcat
    /app/list/data/apache-tomcat-7.0.75/bin/startup.sh

Arrêt du tomcat
    /app/list/data/apache-tomcat-7.0.75/bin/shutdown.sh
    
Répertoire data de jenkins
    /app/list/data/data_jenkins




### ULE_SERVICES (dev)

`<note tip>`user rabbitmq`</note>`

Répertoire 
    /app/list/valorisations/ULE_SERVICES


### CRON des Purges des Collections

 __I) DEV :__

# DEV

#####

# Purge les jobServeurs datant de plus d'1 an

    00 14 * * *     /app/list/data/tools/admin/was70/ADMPLANET/scripts/CRON/DEV/Purge_JobServeurs.sh 365
# Purge les installedApps datant de plus de 2 ans

    02 14 * * * /app/list/data/tools/admin/was70/ADMPLANET/scripts/CRON/DEV/Purge_InstalledApps.sh 730

# Nettoie les logs générés dans /app/list/data/tools/admin/was70/ADMPLANET/scripts/CRON/logs, et qui datent de plus de 30 jours.

    00 15 * * * /app/list/data/tools/admin/was70/ADMPLANET/scripts/CRON/DEV/Nettoyage_logs.sh 30


 __II) PROD :__

# PROD

######

# Purge les jobServeurs datant de plus d'1 an

    30 14 * * * /app/list/data/tools/admin/was70/ADMPLANET/scripts/CRON/PROD/Purge_JobServeurs.sh 365
# Purge les installedApps datant de plus de 2 ans

    32 14 * * * /app/list/data/tools/admin/was70/ADMPLANET/scripts/CRON/PROD/Purge_InstalledApps.sh 730

# Nettoie les logs générés dans /app/list/data/tools/admin/was70/ADMPLANET/scripts/CRON/PROD/logs, et qui datent de plus de 30 jours.

    30 15 * * * /app/list/data/tools/admin/was70/ADMPLANET/scripts/CRON/PROD/Nettoyage_logs.sh 30

 __III) Arborescence des scripts shell :__

[rabbitmq@ld002mys6883 CRON]$ pwd
    /app/list/data/tools/admin/was70/ADMPLANET/scripts/CRON

[rabbitmq@ld002mys6883 CRON]$ find . -type d

*  . 

*  ./DEV

*  ./DEV/logs

*  ./PROD

*  ./PROD/logs


## lr002app8511  (machine de développement WIP)

`<note tip>`User nominatif dev + sudo mid`</note>`

Adresse IP :
    10.179.227.171

Outils hébergés :
    SVN (dev)


### SVN (dev)

Démarrage Apache
    sudo service httpd start

Arrêt Apache
    sudo service httpd stop

URL
    http://10.179.227.171:82/svn/te/
    
User/password
    svnuser/123456  

Script d'import prod vers dev
    Sur proasul1 : /home/svnadmin/svn-prod-to-dev/import.sh
    
## ld002app8512  (machine de développement WIP)

`<note tip>`User nominatif dev sudo su - appadm01`</note>`

Adresse IP :
    10.179.139.144

Outils hébergés :
 MongoDB (dev)


### MongoDB  (dev)

Instance docker sur la machine ld002app8512
Script de création de l'instance docker : 
`/app/list/data/docker/mongodb-dev/start-mongo-db.sh `
Script d'arrêt/suppression l'instance docker : 
`/app/list/data/docker/mongodb-dev/stop-mongo-db.sh `

l'instance dans l'interface portainer 
http://ld002app8513.cloud.group.gca:9000/#/containers/caf3917e27d0f959a983735266f6cf08310149c94233661925a2aa786c44a60d  

### POC SWARM

 
`<note important>`export DOCKER_HOST="tcp:127.0.0.1:2376" effectué dans le fichier .bash_profile de appadm01 `</note>` 
    
    cat /etc/docker/daemon.json

    {
    "live-restore": true 
    }

###### > J'ai passé le live-restore à false car incompatible avec Docker Engine swarm mode


    sudo service docker start


Sur la machine 8513

    => docker swarm init


Sur la machine 8512

    docker swarm join \ 
    --token SWMTKN-1-2jyxzg29xrr0szimz4ql0zo27xt44rmknrypa8dv4y4u6j731a-et65alizmy7cnsw76lhhk6fr2 \ 
    10.179.139.145:2377 


Pour retrouver la commande join avec le bon token, executer la commande ci-dessous sur le master (8513) : 

    docker swarm join-token worker


Commandes utiles 

    docker node ls  
###### > permet de voire les noeuds du cluster 

    docker service ls 
###### > listes les services (containers) lancées et gérées par swarm 

    docker service ps *service_name* 
###### > permets de voir tous les containers lancés pour un service 

 
Poc en cours : 

    /app/list/data/docker/poc-swarm-java1/Dockerfile 


Construire l'image (à faire manuellement sur les 2 machines pour le moment) 

    => docker build -t poc-swarm-java1 . 

Créer le service avec une seule instance 

    => docker service create --publish 8080:8080 --name poc-swarm-java1 poc-swarm-java1 

Augmenter le nombre d'instance 

    => docker service update poc-swarm-java1 --replicas 10
    ou
    => docker service scale poc-swarm-java1=10 

## ld002app8513  (machine de développement WIP)

`<note tip>`User nominatif dev sudo su - appadm01`</note>`

Adresse IP :
    10.179.139.145

Outils hébergés :
 Portainer

### PORTAINER

Créer le service

`<note important>`
URL : http://ld002app8513.cloud.group.gca:9000/
----
PASSWORD: adminswarm
`</note>`

    docker service create \
    --name portainer \
    --publish 9000:9000 \
    --constraint 'node.hostname == LD002APP8513' \
    --mount type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock \
    --mount type=bind,src=/app/list/data/portainer,dst=/data \
    portainer/portainer \
    -H unix:///var/run/docker.sock

## SAS de production

### proasmav1 

Adresse IP :
    10.240.64.57 (login + mdp KeePass)

Path du sas SICLA :
    /app/list/exportbysib/JEE/

### proasmav2

Adresse IP :
    10.240.64.56 (login + mdp KeePass)

Path du sas SICLA :
    /app/list/exportbysib/JEE/

### LP002MID8524

Adresse IP :
    10.179.225.69 (etudeadm + mdp KeePass)

Path du sas SICLA :
    /app/list/exportbysib/JEE/
    
### LP002MID8523

Adresse IP :
    10.179.225.68 (etudeadm + mdp KeePass)

Path du sas SICLA :
    /app/list/exportbysib/JEE/
## Tips & Tricks

### Root sur machine RH
Pour passer root sur une machine RH, faire les manips' suivantes :
sudo /bin/vi /tools/list/product/apache*/bin/*ctl/toto.txt
puis
:shell dans le vi
on sort en tant que root

### Problème accès SSH par clé RSA

Vérifier que : 
 1.  le répertoire home du user n'est pas en 777 (755 par exemple)
 2.  le répertoire ~/.ssh est à 700 max
 3.  le fichier ~/.ssh/authorized_keys est à 600
## CDAAS

Api Rest permettant de lancer des jobs de création/suppression/start/stop de serveur, socle was ou tomcat.
L'api permet aussi de lancer des jobs de déploiement, de création de ressources (JCA, JDBC...)

Le processus se déroule comme suite, création d'un fichier zip contenant les éléments nécessaires pour l'exécution d'une action.
Le zip contient :

*  des fichiers templates, récupérer depuis le référentiel des templates
http://10.179.226.166/nexus/content/repositories/releases/com/lcl/was85/ibm/was-template/1.6/was-template-1.6.zip 


*  un fichier tokens.props qui contient des informations sous forme de clés/valeurs nécessaire à l'exécution des actions, par exemple nom du serveur, nom de l'instance, le fqdn...etc.


*  s'il s'agit d'un déploiement, le zip contient également l'ear à déployer

Le processus CDAAS est implémenté dans le projet jobscheduler 
http://git-webdev.ib.fr.cly:7000/summary/ul%2Fjobscheduler.git
Classe java principale : CdaasRunner 

La carto ou l'ihm de déploiement post un job dans mongo qui est ensuite consommé par le jobscheduler.

La création d'un serveur se fait depuis la carto, onglet Serveur, bien prendre soin de choisir la typologie CDAAS

La suppression et le restart d'un serveur se fait également depuis la carto, choisir serveur de typologie CDAAS, bouton de suppression et redémarrage.

Le déploiement se fait via l'ihm de déploiement, il faut au préalable avoir l'habilitation nécessaire pour effectuer un déploiement sur un serveur CDAAS. Ensuite le déploiement se fait comme pour un processus normal. 

## Deltainstaller

source :http://subversion-webdev.ib.fr.cly/ul/DeltaInstaller/trunk
déploiement via jenkins : http://jenkins-webdev.ib.fr.cly/jenkins/job/deltaInstaller_deploy/


## Docker Swarm SILCA

Informations d'accès à la plateforme: 


*  lclhp-swarm-01, ip: 10.56.137.7 

*  lclhp-swarm-02, ip: 10.56.137.8

*  lclhp-swarm-03, ip: 10.56.137.9 

Compte pour faire du SSH : admin / fuolie0Gee

Commandes pour pousser une image :


*  docker login  registry-lcl.sws.group.gca  --username lcluser  --password PEelKlSt1aph

*  docker tag 0d86bc8a4a02 registry-lcl.sws.group.gca/testpush[:0.0.1]

	REPOSITORY                            TAG                 IMAGE ID            CREATED             SIZE
	testpush                              latest              0d86bc8a4a02        2 days ago          122 MB
	registry-lcl.sws.group.gca/testpush   0.0.1               0d86bc8a4a02        2 days ago          122 MB


*  docker push registry-lcl.sws.group.gca/testpush[:0.0.1]

## Purge du Thinpool docker (devicemapper redhat/centOS/fedora)

[C'est par ici que ça se passe...](private/dockerpurgethinpool)







