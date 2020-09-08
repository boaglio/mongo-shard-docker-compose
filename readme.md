Mongo Sharded Cluster com Docker Compose
=========================================

Um exemplo de cluster de MongoDB com shard usando fator de replication 2 com `docker` usando `docker-compose`.

Ele é rápido e simples apenas para teste, não seja tonto de usar isso em prod. 

Fork de ótimo trabalho de [https://github.com/chefsplate/mongo-shard-docker-compose](https://github.com/chefsplate/mongo-shard-docker-compose)

## Componentes do MongoDB


* Config Server (replica set com 3 membros): `config01`,`config02`,`config03`

* 3 Shards (cada um com um replica set de 2 membros):
	* `shard01a`,`shard01b`
	* `shard02a`,`shard02b`
	* `shard03a`,`shard03b`

* 1 Query Router (mongos): `router`

## Setup  

### Subindo todos os containers

```
# docker-compose up
```

### Inicializar os replica sets do config server

```
# sh 1-init-config-server.sh
```

### Inicializar os replica sets dos shards

```
# sh 2-init-shard1.sh
# sh 3-init-shard2.sh
# sh 4-init-shard3.sh
```

### Inicializar o query router

```
# sh 5-init-query-router.sh
```

### Verificando o status do sharded cluster

```
# mongo
mongos> sh.status()
--- Sharding Status --- 
  sharding version: {
        "_id" : 1,
        "minCompatibleVersion" : 5,
        "currentVersion" : 6,
        "clusterId" : ObjectId("5f558af95d69c51c51f98b53")
  }
  shards:
        {  "_id" : "shard01",  "host" : "shard01/shard01a:27018,shard01b:27018",  "state" : 1 }
        {  "_id" : "shard02",  "host" : "shard02/shard02a:27019,shard02b:27019",  "state" : 1 }
        {  "_id" : "shard03",  "host" : "shard03/shard03a:27020,shard03b:27020",  "state" : 1 }
  active mongoses:
        "4.2.8" : 1
  autosplit:
        Currently enabled: yes
  balancer:
        Currently enabled:  yes
        Currently running:  no
        Failed balancer rounds in last 5 attempts:  0
        Migration Results for the last 24 hours: 
                63 : Success
  databases:
        {  "_id" : "config",  "primary" : "config",  "partitioned" : true }
                config.system.sessions
                        shard key: { "_id" : 1 }
                        unique: false
                        balancing: true
                        chunks:
                                shard01 961
                                shard02 31
                                shard03 32
                        too many chunks to print, use verbose if you want to force print
```
 
### Acessando pelo Mongo Shell

```
# mongo
```

### Removendo o cluster

Para remover todos os dados do cluster:

```
docker-compose stop
docker-compose rm
```
