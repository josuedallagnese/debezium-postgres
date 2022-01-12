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

2) Suba o compose
```
docker-compose up
```

3) Acesse a API do debezium/conector e cadastre o conector para o banco PostgreSQL. No meu caso estou utilizando o Azure Database for PostgreSQL server.
Confirme que o banco está com a configuração de replicação logica habitadada.
Maiores detalhes em: https://debezium.io/documentation/reference/connectors/postgresql.html
No Azure PostgreSQL essa opção se encontra na aba "Replication" da sua instância, opção "LOGICAL".

Após isso crie o conector via API com os valores que você deseja:

```
- Substituir os parametros antes de criar o conector:

database.hostname
database.user
database.password
table.include.list

curl -i -X POST -H "Accept:application/json" -H  "Content-Type:application/json" http://localhost:8083/connectors/ -d @connector.json
```



# Para limpar o ambiente docker
```
docker-compose down
```

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


# Referências
https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-kafka-connect-tutorial
https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect
https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-kafka-connect-debezium
https://debezium.io/documentation/reference/1.7/configuration/signalling.html
