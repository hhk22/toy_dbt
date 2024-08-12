
# DBT Installation

## Install Packages

```
pip install dbt-core==1.2.0
pip install dbt-postgres==1.2.0

vi init.sql
>>
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO users (username, email) VALUES ('alice', 'alice@example.com');
INSERT INTO users (username, email) VALUES ('bob', 'bob@example.com');
INSERT INTO users (username, email) VALUES ('carol', 'carol@example.com');


>> dockerFile
FROM postgres:latest

ENV POSTGRES_USER="kwon"
ENV POSTGRES_PASSWORD="kwon"
ENV POSTGRES_DB="table"

EXPOSE 5432

COPY init.sql /docker-entrypoint-initdb.d/

CMD ["postgres"]

docker build -t my_postgres .
docker run --name local-postgres -e POSTGRES_USER=myuser -e POSTGRES_PASSWORD=mypassword -e POSTGRES_DB=mydb -p 5432:5432 -d postgres

```

## Dbt project settings

```
vi ~/.dbt/profile.yml
>>
my_project:
  outputs:
    dev:
      dbname: table
      host: localhost
      password: kwon
      port: 5432
      schema: schema
      threads: 1
      type: postgres
      user: kwon
  target: dev

cd ~
dbt init my_project
vi ~/my_project/dbt_project.yml
>>
name: 'my_project'
version: '1.0.0'

profile: 'my_project'

model-paths: ["models"]
analysis-paths: ["analyses"]
test-paths: ["tests"]
seed-paths: ["seeds"]
macro-paths: ["macros"]
snapshot-paths: ["snapshots"]

clean-targets:
  - "target"
  - "dbt_packages"

models:
  my_project:
    example:
      +materialized: view
```

## Test

```
dbt debug
>> 00:51:22  All checks passed!

```