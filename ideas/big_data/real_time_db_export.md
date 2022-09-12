# Real time challenge
The ERP system at X has a PG database running in the background.
We want to have in near real-time data from the ERP in the DWH. For this purpose we have built the following data pipeline:
- We have activated Change Data Capture on the PG instance
- We have set up a VM on which we are running Debezium. Debezium collects the change events published by the PG DB and pushes them Apache-Kafka.
- Read the messages and process them as needed.

## Debezium
Debezium is an open source distributed platform for change data capture. Start it up, point it at your databases, and your apps can start responding to all
of the inserts, updates, and deletes that other apps commit to your databases. Debezium is durable and fast, so your apps can respond quickly and
never miss an event, even when things go wrong.

### How it works
This example was taken from the original debezium documentation https://github.com/debezium/debezium-examples/tree/main/tutorial

```
export DEBEZIUM_VERSION=1.9
docker-compose up
```

### Start Postgres connector
```
curl -i -X POST -H "Accept:application/json" -H  "Content-Type:application/json" http://localhost:8083/connectors/ -d @register-postgres.json
```
### Consume messages from a Debezium topic
```
docker-compose exec kafka /kafka/bin/kafka-console-consumer.sh \
    --bootstrap-server kafka:9092 \
    --from-beginning \
    --property print.key=true \
    --topic dbserver1.inventory.customers
```
### Modify records in the database via Postgres client
```
docker-compose -f docker-compose-postgres.yaml exec postgres env PGOPTIONS="--search_path=inventory" bash -c 'psql -U $POSTGRES_USER postgres'
```
