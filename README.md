# Overview

This project is used for PingOne Authorize tutorials on API access management and fine-grained authorization.

# What's Included

This is a docker-compose project containing a Kong API Gateway,
its PostgreSQL database (for its configuration),
and the Ping authorization plugin for Kong already installed.
Additionally, Kong is configured to proxy REST API to the
[Meme Game API](https://meme-game.com), a toy API simple yet flexibile enough
to demonstrating several authorization-related use cases.

> _NOTE:_ This project using the Kong API Gateway (i.e. the enterprise version, not the open source version)
in "[Free mode](https://konghq.com/pricing/)". By using this project you agree to the terms of their
[software license](https://konghq.com/kongsoftwarelicense/).

# Setup

## Requirements

* Docker and docker-compose
* cURL (or something) for verifying connectivity

## Configuration

Copy `env-template.txt` to `.env` and customize for your environment.

This project requires 3 ports:
* `KONG_ENGINE_HTTPS_PORT` - The runtime port for proxying REST API requests, by default 8443
* `KONG_ADMIN_GUI_PORT` - The port for the Kong Manager UI, by default 8002
* `KONG_ADMIN_API_PORT` - The port for the Kong Admin API, by default 8001

> _NOTE:_ This project supports `docker-machine`, if you are inclined.
See `env-template.txt` for more information.

# Running

## Starting

> ***WARNING:*** This project exposes the Kong Manager UI and the Kong Admin API
**WITHOUT ANY ADMIN AUTHENTICATION**. Be careful if you are running
this project on a docker machine exposed to the internet.

1. Bring everything up

```
docker-compose up -d --build
```

2. Access the admin GUI

```
open http://localhost:8002
```

> If you have no default workspace and the "New Workspace" button is disabled then something went wrong.

3. Test that it can connect to the Meme Game API

```
curl -i --insecure --oauth2-bearer '{"sub":"user.0"}' https://localhost:8443/meme-game/api/v1/users/user.0/answers
```

> You should get a HTTP 200 status and a JSON response. If not, something went wrong.

## Stopping

To stop Kong and delete the Postgres database:

```
docker-compose down -v
```

## Troubleshooting

To increase logging verbosity and view the log files:

```
docker-compose stop kong
vi .env # set KONG_LOG_LEVEL=debug
docker-compose start kong
docker-compose logs -f kong
```
