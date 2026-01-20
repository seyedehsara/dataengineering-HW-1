# dataengineering-HW-1
Question 1. Understanding Docker images
Run docker with the python:3.13 image. Use an entrypoint bash to interact with the container.

What's the version of pip in the image?

```bash
docker run python:3.13 pip --version

```
answer : 25.3 

Question 2. Understanding Docker networking and docker-compose
Given the following docker-compose.yaml, what is the hostname and port that pgadmin should use to connect to the postgres database? (check the main file for complete question)

Spin that docker-compose.yml with docker compose up -d
Log into pgadmin container with: docker exec -it pgadmin bash
Test connectivity with nc

```bash
a9f4522e9e0b:/pgadmin4$ nc -zv db 5432
db (172.18.0.3:5432) open

a9f4522e9e0b:/pgadmin4$ nc -zv postgres 5432
postgres (172.18.0.3:5432) open


```
Both the service name (db) and the container name (postgres) can be used. You should be aware that the port being used is the one exposed by the container (5432), not the port is set as port-forwarding (5433)

Answer: postgres:5432 or db:5432


Question 3. Counting short trips

# Environment Setup & Data Preparation

This project uses PostgreSQL and pgAdmin for querying NYC Green Taxi trip data from November 2025.
All queries were written in SQL using pgAdmin's query tool. Below are the setup and preparation steps.

Install PostgreSQL and pgAdmin

Download PostgreSQL from https://www.postgresql.org/download/windows/

During installation:

Set a secure password 

Make sure pgAdmin is selected for installation

After installation, open pgAdmin 4 from the Start Menu

 Create a PostgreSQL Database

Open pgAdmin

Connect to your PostgreSQL server using your password

Right-click on Databases → Create → Database

Name it: ny_taxi

Create Tables in PostgreSQL
Table: green_taxi_data

Create this table in pgAdmin using the GUI:
ny_taxi → Schemas → public → Tables → Create → Table

Import CSV into PostgreSQL using Python

GUI import failed due to file permission issues, so I used pandas and SQLAlchemy also I use same approach to import taxi_zone_lookup.csv.

At this point, both tables — green_taxi_data and taxi_zone_lookup  are ready to be queried using SQL in pgAdmin!

Counting short trips

For the trips in November 2025 (i.e., lpep_pickup_datetime between '2025-11-01' and '2025-12-01'),
how many trips had a trip_distance of less than or equal to 1 mile?


```bash
SELECT COUNT(*) AS short_trip_count
FROM green_taxi_data
WHERE trip_distance <= 1
  AND lpep_pickup_datetime >= '2025-11-01'
  AND lpep_pickup_datetime < '2025-12-01';

```
answer : 8007

Question 4. Longest trip for each day
Which was the pick up day with the longest trip distance? Only consider trips with trip_distance less than 100 miles (to exclude data errors).
Use the pick up time for your calculations.

```bash
SELECT 
  DATE(lpep_pickup_datetime) AS pickup_date,
  MAX(trip_distance) AS max_trip_distance
FROM green_taxi_data
WHERE trip_distance < 100
GROUP BY pickup_date
ORDER BY max_trip_distance DESC
LIMIT 1;

```

answer : 2025-11-14  max-trip-dis : 88.03

Question 5. Biggest pickup zone
Which was the pickup zone with the largest total_amount (sum of all trips) on November 18th, 2025?

```bash
SELECT 
    z.zone AS pickup_zone,
    SUM(g.total_amount) AS total_revenue
FROM green_taxi_data g
JOIN taxi_zone_lookup z
    ON g.pu_locationid = z.locationid
WHERE DATE(g.lpep_pickup_datetime) = '2025-11-18'
GROUP BY z.zone
ORDER BY total_revenue DESC
LIMIT 1;

```

answer : east harlem north


Question 6. Largest tip
For the passengers picked up in the zone named "East Harlem North" in November 2025, which was the drop off zone that had the largest tip?

Note: it's tip , not trip. We need the name of the zone, not the ID.

```bash
SELECT 
    dropoff_zone.zone AS dropoff_zone,
    g.tip_amount
FROM green_taxi_data g
JOIN taxi_zone_lookup pickup_zone
    ON g.pu_locationid = pickup_zone.locationid
JOIN taxi_zone_lookup dropoff_zone
    ON g.do_locationid = dropoff_zone.locationid
WHERE pickup_zone.zone = 'East Harlem North'
  AND g.lpep_pickup_datetime >= '2025-11-01'
  AND g.lpep_pickup_datetime < '2025-12-01'
ORDER BY g.tip_amount DESC
LIMIT 1;

```
answer : yorkville west  tip-amount: 81.89


Question 7. Terraform Workflow
Which of the following sequences, respectively, describes the workflow for:

Downloading the provider plugins and setting up backend,
Generating proposed changes and auto-executing the plan
Remove all resources managed by terraform

Downloading the provider plugins and setting up backend:

terraform init
Generating proposed changes and auto-executing the plan:

terraform apply -auto-approve
Remove all resources managed by terraform`

terraform destroy
Answer:

terraform init, terraform apply -auto-approve, terraform destroy
