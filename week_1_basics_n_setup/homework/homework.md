# Homework

## Question 1

To get help on the "docker build" command we run `docker build --help`.

We then see one of the otpions is `--iidfile string` with the text "Write the image ID to the file".

## Question 2

To run docker with the python:3.9  image in an interactive mode and bash entrypoint we use the following command:

`docker run -it --entrypoint=bash python:3.9`. (`-t` is needed as a terminal driver)

We then run `pip list` to see there are 3 packages installed.

## Prepare postgres

First we need to run postgres which we do with the following command:

```
docker run -it \
    -e POSTGRES_USER="root" \
    -e POSTGRES_PASSWORD="root" \
    -e POSTGRES_DB="ny_taxi" \
    -v $(pwd)/ny_taxi_postgres_data:/var/lib/postgresql/data/ \
    -p 5432:5432 \
    --network=pg-network \
    --name pg-database-2 \
    postgres:13
```

The jupyter notebook `upload-data.ipynb` uploads this data to the server.

We then need to connect to the postgres connection in the terminal using pgcli.

It is also possible to connect via pgadmin - I will demonstrate how to do both for all the questions.

### Using Pgcli

TO connect to the postgres connection through pgcli we run the following command:

```
pgcli -h localhost -p 5432 -u root -d ny_taxi
```


### Using pgadmin

```
docker run -it \
    -e PGADMIN_DEFAULT_EMAIL="admin@admin.com" \
    -e PGADMIN_DEFAULT_PASSWORD="root" \
    -p 8080:80 \
    --network=pg-network \
    --name pgadmin-2 \
    dpage/pgadmin4
```

We then need to start a network so that pgadmin and the postgres server connect to each other.

```
docker network create pg-network
```

## Question 3

The following PostgreSQL syntax tells us how many trips were made on January 15.

```
SELECT
    COUNT(*)
FROM
    green_taxi_data
WHERE
    DATE(lpep_pickup_datetime) = '2019-01-15' AND
    DATE(lpep_dropoff_datetime) = '2019-01-15'
;
```

This gives us the answer 20530.

## Question 4

The following PostgreSQL syntax tell us which day had the largest trip distance

```
SELECT
    DATE(lpep_pickup_datetime) AS day,
    trip_distance
FROM
    green_taxi_data
ORDER BY
    trip_distance DESC
LIMIT
    1
;
```

It was the 15th January 2019.

## Question 5

The following SQL syntax tells us how many trips had 2 and 3 passengers on the 1st January:

```
SELECT
	passenger_count,
	COUNT(*) AS number
FROM
    green_taxi_data
WHERE
    DATE(lpep_pickup_datetime) = '2019-01-01'
GROUP BY
	passenger_count
;
```

We can see that there were 1282 trips with 2 passengers and 254 trips with 3 passengers.

### Question 6

The following SQL syntax tells us for all the passengers picked up in the Astoria Zone which was the drop off zone that had the largest tip.

```
SELECT
	tzl2."Zone"
FROM
	green_taxi_data gtd
LEFT JOIN
	taxi_zone_lookup tzl
ON
	gtd."PULocationID" = tzl."LocationID"
LEFT JOIN
	taxi_zone_lookup tzl2
ON
	gtd."DOLocationID" = tzl2."LocationID"
WHERE
	tzl."Zone" = 'Astoria'
ORDER BY
	gtd.tip_amount DESC
LIMIT 1
;
```

The answer is Long Island City/Queens Plaza.