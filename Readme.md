# Debezium + Postgres + Event Hubs
 
Exemplo de utilização do Debezium + PostgresSQL + Event Hubs

Pré-requisito: docker.

# Para executar

1) Crie um arquivo ".env" na raiz do diretório com as váriaveis de ambiente. Exemplo:
``` .env
DEBEZIUM_VERSION=1.8
EH_NAME=eventhub-dev
EH_CONNECTION_STRING=Endpoint=sb://eventhub-dev.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXX
```
Ou então passe os valores ao executar o docker-compose

2) Suba o compose
```
docker-compose up
```

3) Cadastre o conector via API
- Neste caso estou utilizando o Azure Database for PostgreSQL server.
- Confirme a configuração do banco se esta com a replicação logica habitadada.
- Detalhes: https://debezium.io/documentation/reference/connectors/postgresql.html.
- No Azure PostgreSQL essa opção se encontra na aba "Replication" da sua instância: opção "LOGICAL".

Após isso crie o conector via curl ou como preferir:

```
- Substituir os parametros antes de criar o conector:

database.hostname
database.user
database.password
table.include.list

curl -i -X POST -H "Accept:application/json" -H  "Content-Type:application/json" http://localhost:8083/connectors/ -d @connector.json
```

### Observações:
- O Debezium irá criar um slot de replicação dentro do postgres chamado "debezium", mas você pode configurar através do parametro "slot.name" do conector.
- Serão gerados 3 hubs/topicos dentro do Event Hubs respectivamente chamados de postgres-dev-configs, postgres-dev-offsets e postgres-dev-status. Eles podem ser configurados dentro do docker-compose alterando as váriaveis: CONFIG_STORAGE_TOPIC, OFFSET_STORAGE_TOPIC, STATUS_STORAGE_TOPIC.
- Esses topicos serão usados pelo Debezium para controlar o estado dos conectores cadastrados e o ponto em que a leitura foi realizada até o momento: offset.

### Para conferir o slots de replicação que o Debezium irá criar dentro do postgres:
```
select * from pg_replication_slots;
```

### Para deletar esses slots de replicação:
```
select pg_drop_replication_slot('debezium');
```

### Conector verificar o status de um conector:
http://localhost:8083/connectors/postgres-connector
http://localhost:8083/connectors/postgres-connector/status

# Para limpar o ambiente docker
```
docker-compose down
```

# Referências
https://debezium.io/documentation/reference/connectors/postgresql.html
https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-kafka-connect-tutorial
https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect
https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-kafka-connect-debezium
https://debezium.io/documentation/reference/1.7/configuration/signalling.html
