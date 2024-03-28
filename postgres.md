1. `su - postgres` - to login as postgres inside the VPS
1. `createuser neil` - to create the user (in bash not psql)
1. Once logged in enter the following to enter psql:  `psql`
1. `create database bpsimple with owner neil` - to create the database with owner
1. `\i data/beg-dbs-w-postgresql-master/UNIX/Chapter03/create_tables-bpsimple.sql ` - to execute the SQL commands in the file.
1. `\c bpsimple neil` - connect to bpsimple db as neil user
1. `\l` - to list all the dbs
1. `\dt` - to list tables in the current db
1. `\conninfo` - to fetch the status
1. `\q` - to exit
1. `psql -U neil -d bpsimple` - connect to bpsimple as user neil.
1. `$ psql -h localhost -p 5432 -U neil -d bpsimple` - to connect to docker postgres from host.
1. `$ pgbench -c 10 -t 1000 -C -U neil -h localhost -p 6432 bpsimple` - 