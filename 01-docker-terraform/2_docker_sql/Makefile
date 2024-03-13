network:
	podman network create ny_taxi_network

run-postgres:
	podman run -it \
	-e POSTGRES_USER=root \
	-e POSTGRES_PASSWORD=root \
	-e POSTGRES_DB=ny_taxi \
	-v $(shell pwd)/ny_taxi_postgres_data:/var/lib/postgresql/data \
	-d -p 5433:5432 \
	--network ny_taxi_network \
	--name ny_taxi_postgres \
	postgres:13

# connect to the postgres container in pgadmin with host/address: ny_taxi_postgres
# and port: 5432 (docker container port) and user: root and password: root, maintenance database is postgres
run-pgadmin:
	podman run -it \
	-e PGADMIN_DEFAULT_EMAIL=admin@admin.com \
	-e PGADMIN_DEFAULT_PASSWORD=admin \
	-d -p 5050:80 \
	--network ny_taxi_network \
	--name ny_taxi_pgadmin \
	dpage/pgadmin4

pgcli:
	pgcli -h localhost -p 5433 -U root -d ny_taxi

ingest:
	python3 ingest_data.py \
	--user=root \
	--password=root \
	--host=localhost \
	--port=5433 \
	--db=ny_taxi \
	--table_name=yellow_taxi_trips \
	--url=https://github.com/DataTalksClub/nyc-tlc-data/releases/download/yellow/yellow_tripdata_2021-01.csv.gz

build:
	podman build -t taxi_ingest:v001 .

run:
	podman run -it \
	--network=ny_taxi_network \
	taxi_ingest:v001 \
	--user=root \
	--password=root \
	--host=ny_taxi_postgres \
	--port=5432 \
	--db=ny_taxi \
	--table_name=yellow_taxi_trips \
	--url=https://github.com/DataTalksClub/nyc-tlc-data/releases/download/yellow/yellow_tripdata_2021-01.csv.gz
