## PRE-REQUISITES
1. `docker-compose up`
2. `docker exec -it django-crud-operations-using-postgresql-web-1 python src/manage.py migrate`

## IMPORTANT URLs
Django App: http://localhost:8000
Connect UI: http://localhost:8080 
Kafka UI: http://localhost:8081
For more info, check `docker-compose.yml`

## TESTING
To directly see the WAL events, create a new logical slot and start trailing. Do this from inside the postgres DB container so that you have everything inside the container.
```
# Login inside the docker container
docker exec -it my_db /bin/bash

# Create a new logical slot
PG_PASSWORD=great_password pg_recvlogical -U great_user -d great_db -p 5432 -h great_db --create-slot --slot manual_trail -P wal2json

# Connect to the created slot and start trailing events
PG_PASSWORD=great_password pg_recvlogical -U great_user -d great_db -p 5432 -h great_db --slot manual_trail --start -o pretty-print=1 -f -

# Make changes in database either from frontend or backend
```

To go the debezium route,
1. Create a new PostgreSQL connector.
2. Enter following JSON
```
{
  "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
  "database.user": "great_user",
  "database.dbname": "great_db",
  "database.hostname": "great_db",
  "database.password": "great_password",
  "name": "crud-postgres-db-connector",
  "database.server.name": "crud.postgres.db"
}
```
If you go the form route,
Name: Give some name
Namespace: crud.postgres.db (Could be anything as long as it's unique because namespace)
Hostname: great_db
Other things should be self obvious or optional.
