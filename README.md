# Logging service

This is logging service for the rest of services log against docker.

## Getting Started

These instructions will allow you to clone the project and run it  on your local machine for development and testing purposes. See the "Deployment" section for notes on how to deploy the service on a live system.

### Prerequisites

You need to install Docker and Docker compose on your machine.

For documentation about this, see [Docker](https://www.docker.com/) - [Docker compose](https://docs.docker.com/compose/) 
 
You need to clone the repository to your local environment.

Open a console and go to the directory which you want to be the parent of all the microservices, e.g. (user)/git/, then paste the next command: 

```
git clone ssh://git@bitbucket.endava.com:7999/padmsp/logging.git
```

### Installing

Keep in mind, This microservice runs using Eureka, so starting that service is needed before any other microservice.

After start the Discovery Server (a.k.a Eureka Registry, or simply Eureka), you can run the Logging microservice.

## If everything goes well...

You can start to the kibana console in [localhost](http://localhost:5601/app/kibana) to searching logs of your services.

## Examples of what this microservice does, and their uses

1. See the logs of all the services which runs under docker.
2. Can make queries to search some types of logs.
3. Logs can filtered by services, date, or any kind of word.

## Deployment

   The logging microservice, is deployed in Docker, to determine the functionality of all the structure of microservices running in whatever OS.
   
   The way to start this microservice is using docker-compose (we'd created a repository with an orchestration, with docker-compose).
   Using a console, go to orchestration folder and type:
   ```
   docker-compose up --build filebeat
   ```
   You can use '-d' before the name of the microservice 'filebeat', to continue using the same console for another purpose, hidding the log.
   E.g.:
   ```
   docker-compose up -d --build filebeat
   ```
This is the structure of docker-compose to the service of logging microservice:
```
 filebeat:
     hostname: filebeat
     build:
       context: ../../../../logging/filebeat
     volumes:
       - "/var/lib/docker/containers:/usr/share/dockerlogs/data:ro"
       - "/var/run/docker.sock:/var/run/docker.sock"
     links:
       - logstash
       - elasticsearch
       - kibana
 
   kibana:
     image: docker.elastic.co/kibana/kibana:6.5.3
     environment:
       - "LOGGING_QUIET=true"
     links:
       - elasticsearch
     ports:
       - 5601:5601
     restart: on-failure
 
   logstash:
     hostname: logstash
     build:
       context: ../../../../logging/logstash
     ports:
       - 5044:5044
     environment:
       LOG_LEVEL: error
     links:
       - elasticsearch
     restart: on-failure
 
   elasticsearch:
     hostname: elasticsearch
     build:
       context: ../../../../logging/elasticsearch
     environment:
       - cluster.name=pad-b-elasticsearch-cluster
       - bootstrap.memory_lock=true
       - "ES_JAVA_OPTS=-Xms256m -Xmx256m"
     ulimits:
       memlock:
         soft: -1
         hard: -1
     ports:
       - 9200:9200
     restart: on-failure
```


## Built With

* [Elasticsearch](https://www.elastic.co/) - Provides real-time search and analytics for all types of data.
* [Logstash](https://www.elastic.co/products/logstash) - Centralize, transform & stash your data
* [Kibana](https://www.elastic.co/es/products/kibana) - Your window into the Elastic Stack
* [Filebeat](https://www.elastic.co/es/products/beats/filebeat) - Lightweight Shipper for Logs


## Versioning

We use [Bitbucket](https://bitbucket.org/product/) for versioning. For the versions available, see the [tags on this repository](https://bitbucket.endava.com/projects/PADMSP/repos/logging/browse). 
Every push to the repository, triggers an integrated test in [jenkins](https://jenkins.endava.net/job/Endava/job/MS-Platform/job/Logging/)
## Authors

* **ENDAVA Paraná Back-end Community** - *Initial work* - [PADB-Community](https://confluence.endava.com/display/DevDisc/Backend+Parana+Community)




