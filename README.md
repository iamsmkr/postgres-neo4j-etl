# postgres-neo4j-etl

Head to [WIKI](https://github.com/iamsmkr/postgres-neo4j-etl/wiki) for documentations.

## Deploy
- To start containers for the first time we could use the following command:
  ```sh
  $ docker-compose up
  ```
  This will start all the containers and initialise postgres and neo4j.
  
  **Note:** To avoid reinitializing databases, we can restart the service by specifying relevant services with the docker-compose command. No need to start ETL container if data already etl'd to neo4j.
  ```sh
  $ docker-compose up postgres pgadmin neo4j graphql 
  ```

- ETL container service needs to be started separately. Note that ETL must be started only after postgres is initialized completely.
  ```sh
  $ cd etl-postgres-neo4j
  $ sbt docker:publishLocal
  $ docker run --net="host" --env ETL_MINIMAL=true --name=imdb_etl etl-postgres-neo4j:0.1
  ```
  Notice the environment variable `ETL_MINIMAL` is set to `true`. This will ETL the minimal (configurable) dataset from postgres to neo4j. The minimun number of movies that you wish to initialize neo4j property graph with is configured with environment variable `MINIMAL_DATASET_SIZE=10` under `postgres-init`. When postgres is initialized the complete dataset is downloaded from IMDB interfaces and pertaining denormalized tables are created. However, based on the the configured environment variable `MINIMAL_DATASET_SIZE` a minimal dataset is also created alongside. Subsequently, when ETL container is started with `ETL_MINIMAL` set to `true`, data from the minimal set is etl'd to neo4j instead. Should you choose to etl complete dataset, set `ETL_MINIMAL` to `false` and restart etl container. This is done to quickly have a sneak peek into the system i.e., how it is set up and how it works. 
