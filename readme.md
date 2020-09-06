Mongo Sharded Cluster com Docker Compose
=========================================

Um exemplo de cluster de MongoDB com shard usando fator de replication 2 com `docker` usando `docker-compose`.

Ele é rápido e simples apenas para teste, não seja tonto de usar isso em prod. 

Fork de ótimo trabalho de [https://github.com/chefsplate/mongo-shard-docker-compose](https://github.com/chefsplate/mongo-shard-docker-compose)

### Componentes do MongoDB

* Config Server (replica set com 3 membros): `config01`,`config02`,`config03`

* 3 Shards (cada um com um replica set de 2 membros):
	* `shard01a`,`shard01b`
	* `shard02a`,`shard02b`
	* `shard03a`,`shard03b`

* 1 Query Router (mongos): `router`

### Setup inicial 

**Subindo todos os containers** 

```
docker-compose up
```

** Inicializar os replica sets (config server e shards) and router**

```
sh init.sh
```

O script usa `sleep 20` para esperar o config server e os shards para eleger os primários antes de chamar o query router.

**Verificando o status do sharded cluster**

```
docker-compose exec router mongo
mongos> sh.status()
--- Sharding Status ---
  sharding version: {
	"_id" : 1,
	"minCompatibleVersion" : 5,
	"currentVersion" : 6,
	"clusterId" : ObjectId("5981df064c97b126d0e5aa0e")
}
  shards:
	{  "_id" : "shard01",  "host" : "shard01/shard01a:27018,shard01b:27018",  "state" : 1 }
	{  "_id" : "shard02",  "host" : "shard02/shard02a:27019,shard02b:27019",  "state" : 1 }
	{  "_id" : "shard03",  "host" : "shard03/shard03a:27020,shard03b:27020",  "state" : 1 }
  active mongoses:
	"3.4.6" : 1
 autosplit:
	Currently enabled: yes
  balancer:
	Currently enabled:  yes
	Currently running:  no
		Balancer lock taken at Wed Aug 02 2017 14:17:42 GMT+0000 (UTC) by ConfigServer:Balancer
	Failed balancer rounds in last 5 attempts:  0
	Migration Results for the last 24 hours:
		No recent migrations
  databases:
```

### Startup normal

O cluster precisa ser inicializado apenas na primeira execução.

As próximas execuções podem ser feitas com `docker-compose up` ou `docker-compose up -d`

### Acessando pelo Mongo Shell

```
docker-compose exec router mongo
```

### Removendo o cluster

Para remover todos os dados do cluster:

```
docker-compose stop
docker-compose rm
```
