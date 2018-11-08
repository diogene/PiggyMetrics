## PREPARATION

### netoyage
Pour installer l'ensmble de cette configuration il faut effacer toute la configuration précédente : `docker system prune --volumes --all`

### résolution de nom
Pour simplifier la configuration des modules, il faut mettre tous les noms de host :
```bash
192.168.1.28 	account-mongodb
192.168.1.28 	auth-mongodb
192.168.1.28 	statistics-mongodb
192.168.1.28 	notification-mongodb
192.168.1.28 	rabbitmq
192.168.1.28 	config
192.168.1.28 	registry
192.168.1.28 	gateway
192.168.1.28 	auth-service
192.168.1.28 	account-service
192.168.1.28 	statistics-service
192.168.1.28 	notification-service
192.168.1.28 	monitoring
192.168.1.28 	turbine-stream-service
```

a mettre dans le fichier `/windows/system32/driver/etc/hosts` sous windows et en option de lancement de docker : `-v ./config/src/main/resources/etc:/app/etc` sous linux

### variable d'enrivonnement
lancement :
sous powershell :
```bash
$env:CONFIG_SERVICE_PASSWORD="123456@!"
$env:NOTIFICATION_SERVICE_PASSWORD="123456@!"
$env:STATISTICS_SERVICE_PASSWORD="123456@!"
$env:ACCOUNT_SERVICE_PASSWORD="123456@!"
$env:MONGODB_PASSWORD="123456@!"
docker-compose config
```

sous linux 
```bash
export CONFIG_SERVICE_PASSWORD=123456\@!
export NOTIFICATION_SERVICE_PASSWORD=123456\@!
export STATISTICS_SERVICE_PASSWORD=123456\@!
export ACCOUNT_SERVICE_PASSWORD=123456\@!
export MONGODB_PASSWORD=123456\@!
docker-compose -f docker-compose.infra.yml config
```


## INSTALLATION RACINE

installation du Config
installation du Registry

lancement des services :
docker-compose -f  docker-compose.root.yml up --force-recreate

## INSTALLATION DES BASES DE DONNEES

installation de auth-mongodb
installation de account-mongodb
installation de statistics-mongodb
installation de notification-mongodb

lancement des services :
docker-compose -f  docker-compose.db.yml up --force-recreate


## INSTALLATION DES COMPOSANTS D'INFRA

installation du RabbitMq
installation du Auth Service 

lancement des services :
docker-compose -f  docker-compose.infra.yml up --force-recreate


## INSTALLATION DE L'APPLICATION

installation du Gateway
installation de account-service
installation de statistics-service
installation de notification-service

INSTALLATION DE COMPOSANT DE SUPPORT

installation de monitoring
installation de spring-administrator
installation de turbine-stream-service

installation de ELK


installation de zipkin

curl -sSL https://zipkin.io/quickstart.sh | bash -s
java -jar zipkin.jar

docker run -d -p 9411:9411 openzipkin/zipkin