# Student Course Management — Docker Compose Project

This repository contains a simple Docker Compose setup used in the student-course-management lab. It runs a PostgreSQL database together with two GUI tools (pgAdmin and Adminer) so you can create the `student` table, insert rows, and inspect persistence and networking at the Docker level.

## Contents

- `docker-compose.yml` — Compose file that defines the services, network and named volume used for persistence.
- `.env` — environment variables used by the Compose file (credentials and database name).
- `screenshots/` — screenshots captured while completing the lab (including Docker inspect outputs used below).

## How to run

1. Make sure Docker Desktop (or Docker Engine) is installed and running on your machine.
2. Copy or create a `.env` file in the project root with the following example values (the project includes an example `.env` in screenshots):

```sh
POSTGRES_DB=DB_0001
POSTGRES_USER=junaid
POSTGRES_PASSWORD=0001
PGADMIN_DEFAULT_EMAIL=admin@devops.com
PGADMIN_DEFAULT_PASSWORD=admin
```

1. Start the stack:

```sh
docker compose up -d
```

1. Verify the containers are running:

```sh
docker container ls
```

1. Optional: open the GUIs in your browser:

- Adminer: <http://localhost:8080> (default stack mapping)
- pgAdmin: <http://localhost:5050> (default stack mapping)

## Inspect commands used for evidence

To obtain the diagnostic output used in the screenshots run the inspect commands shown below. Replace the resource names with the names printed by your `docker compose up` run.

Network inspect (example):

```sh
docker network ls
docker network inspect <network_name>
```

Volume inspect (example):

```sh
docker volume ls
docker volume inspect <volume_name>
```

The screenshots included in `screenshots/` show the full JSON output returned by these `inspect` commands.

## What the network inspect output shows

See the screenshot: `screenshots/task7_network_inspect.png` (or similar file in the `screenshots/` folder).

The Docker network inspect JSON includes the network `Name`, `Id`, `Created` timestamp, `Driver` (bridge in this lab), and `IPAM` configuration. It also lists containers currently attached to the network together with their `IPv4Address` and `MacAddress`. From this output you can quickly tell which containers are connected to the network, the subnet used (for example `172.19.0.0/16`) and whether Docker assigned dynamic IPs. The `Containers` block is particularly helpful to map container names to their allocated IPs and endpoint IDs — useful when troubleshooting container-to-container connectivity.

## What the volume inspect output shows

See the screenshot: `screenshots/task7_volume_inspect.png` (or similar file in the `screenshots/` folder).

The Docker volume inspect JSON shows metadata about the named volume including `Name`, `Mountpoint` (the path on the Docker host where the volume data is stored), `Driver` (usually `local`), and `CreatedAt`. The `Mountpoint` is important because it reveals where the database files are persisted on the host; this proves that data written by PostgreSQL survives container restarts when a named volume is used. Labels inserted by Compose (like the project and compose hash) also appear and help identify which Compose project created the volume.

## Notes and troubleshooting

- If `docker compose up` fails because the daemon is not running, ensure Docker Desktop is started and that you can run `docker ps` successfully.
- If you cannot reach the GUIs on the expected ports (5050 and 8080 in the examples), confirm there are no local port conflicts and that the containers report `Up` in `docker container ls`.
- If the `volume inspect` shows an unexpected empty directory, ensure the Postgres container had sufficient permissions and that the container started successfully during the first initialization run.

## Attribution

This README was generated to accompany the student-course-management lab and the provided screenshots in the `screenshots/` folder. If you want any additional detail added (full example inspect JSON pasted inline, or more screenshots embedded), tell me which items to include and I will update this file.
