# Continuous replication PostgreSQL to MySQL using Debezium and JDBC over Kafka Connect

## Motivation

We set a goal to migrate our production database from MySQL to PostgreSQL, but we faced a challenge — we use a BI 
tool called Metabase connected directly to the MySQL, and migrating queries would take significant time.

In order to make this process smoother, this continuous replication was set up to allow us using old MySQL database with the latest data, 
while in production we would already use brand new PostgreSQL.

## Requirements

1. Docker
2. Credentials for the databases

## Setting up

### Connectors

1. Configure your PostgreSQL and MySQL connectors stored in `connect/` directory.

   ```bash
   cp connect/mysql-connector.json.example connect/mysql-connector.json
   cp connect/postgres-connector.json.example connect/postgres-connector.json
   ```

2. Add replication publication to PostgreSQL database. Connect to your database and run:
    
   ```sql
   CREATE PUBLICATION dbz_publication FOR ALL TABLES;
   ```

> If you have a managed database and don't have `root` access, instead of `ALL TABLES`, list the specific tables

### Containers

1. Adjust ports in `.env` (if needed)

1. Start the containers:

   ```bash
   docker compose up -d
   ```

1. Verify plugins have been loaded:

   ```bash
   docker compose exec kafka-connect curl -s  http://localhost:8083/connector-plugins | jq
   ```

1. Add MySQL and PostgreSQL connectors:

   ```bash
   docker compose exec kafka-connect curl -i -X POST -H "Content-Type:application/json" -d @connect/postgres-connector.json http://localhost:8083/connectors
   docker compose exec kafka-connect curl -i -X POST -H "Accept:application/json" -H  "Content-Type:application/json" -d @connect/mysql-connector.json http://localhost:8083/connectors
   ```

1. Verify synchronization process has started by listing topics. You should see topics representing tables in there:

   ```bash
   docker compose exec kafka kafka-topics --list --bootstrap-server localhost:9092
   ```

1. (Optional) Make a safe update in the PostgreSQL database and check in the MySQL the change has been applied.

### Monitoring

- Stream kafka-connect logs

   ```bash
   docker-compose logs -f kafka-connect --tail 100
   ```

- Check connector status

   ```bash
   docker-compose exec kafka-connect curl localhost:8083/connectors/jdbc-sink-mysql/status | jq
   ```

## Resources used

- https://debezium.io/blog/2017/09/25/streaming-to-another-database/
- https://www.mastertheboss.com/jboss-frameworks/debezium/tutorial-using-debezium-jdbc-connector/
- https://rmoff.net/2018/03/24/streaming-data-from-mysql-into-kafka-with-kafka-connect-and-debezium/
- https://medium.com/cstech/streaming-data-from-mysql-with-kafka-connect-jdbc-source-connector-428f4db20b5b
