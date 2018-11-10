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
192.168.1.28 	admin
192.168.1.28 	turbine-stream-service
```

a mettre dans le fichier `/windows/system32/driver/etc/hosts` sous windows et en option de lancement de docker : `-v ./config/src/main/resources/etc:/app/etc` sous linux

### variable d'enrivonnement
Toutes les variables d'environnement disponible 
sous powershell :
```bash
$env:CONFIG_SERVICE_PASSWORD="123456@!"
$env:NOTIFICATION_SERVICE_PASSWORD="123456@!"
$env:STATISTICS_SERVICE_PASSWORD="123456@!"
$env:ACCOUNT_SERVICE_PASSWORD="123456@!"
$env:MONGODB_PASSWORD="123456@!"

$env:MONGODB_AUTH_PORT=25000
$env:MONGODB_ACCOUNT_PORT=26000
$env:MONGODB_STAT_PORT=27000
$env:MONGODB_NOTIF_PORT=28000


$env:MONGODB_AUTH_SERVER=auth-mongodb
$env:MONGODB_ACCOUNT_SERVER=account-mongodb
$env:MONGODB_STAT_SERVER=statistics-mongodb
$env:MONGODB_NOTIF_SERVER=notification-mongodb

AUTH_SERVER
CONFIG_SERVER


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


java -Xms256M -DCONFIG_SERVICE_PASSWORD="123456@!" -jar .\target\config.jar
java -Xms256M -DCONFIG_SERVICE_PASSWORD="123456@!" -jar registry.jar


GET http://registry:8761/eureka/v2/apps
POST http://registry:8761/eureka/v2/apps/config-service HTTP/1.1
content-type: application/json

{
    "instance": {
        "hostName": "config",
        "app": "config-service",
        "vipAddress": "config-service",
        "secureVipAddress": "config-service"
        "ipAddr": "192.168.1.23",
        "status": "STARTING",
        "port": {"$": "8888", "@enabled": "true"},
        "healthCheckUrl": "http://config:8888/actuator/health",
        "statusPageUrl": "http://config:8888/actuator/status",
        "homePageUrl": "http://config:8888"
    }
}

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

java -Xms256M -DMONGODB_AUTH_PORT=25000 -jar auth-service.jar

lancement des services :
docker-compose -f  docker-compose.infra.yml up --force-recreate


## INSTALLATION DE L'APPLICATION

installation de account-service
installation de statistics-service
installation de notification-service
installation du Gateway

java -Xms256M -DMONGODB_ACCOUNT_PORT=26000 -DCONFIG_SERVICE_PASSWORD="123456@!" -DMONGODB_PASSWORD="123456@!" -jar ./account-service.jar 
java -Xms256M -DMONGODB_STAT_PORT=27000 -DCONFIG_SERVICE_PASSWORD="123456@!" -DMONGODB_PASSWORD="123456@!" -jar ./target/statistics-service.jar 
java -Xms256M -DMONGODB_NOTIF_PORT=28000 -DCONFIG_SERVICE_PASSWORD="123456@!" -DMONGODB_PASSWORD="123456@!" -jar ./target/notification-service.jar 

java -Xms256M -DCONFIG_SERVICE_PASSWORD="123456@!" -DNOTIFICATION_SERVICE_PASSWORD="123456@!" -DSTATISTICS_SERVICE_PASSWORD="123456@!"  -DACCOUNT_SERVICE_PASSWORD="123456@!" -jar ./target/gateway.jar 

## INSTALLATION DE COMPOSANT DE SUPPORT

installation de monitoring
installation de spring-administrator
installation de turbine-stream-service

java -Xms256M -DCONFIG_SERVICE_PASSWORD="123456@!" -jar admin.jar 


installation  de prometheus
installation  de grafana


## INSTALLATION DE COMPOSANT DE DIAGNOSTIQUE

installation de ELK

installation de zipkin
docker run -d -p 9411:9411 openzipkin/zipkin