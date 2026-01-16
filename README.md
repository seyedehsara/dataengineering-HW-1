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


