# keycloak-service

This service, a customized instance of [keycloak](https://www.keycloak.org/), centralizes registration, authentication and session lifecycles of all ThePugs users.

## Installation

The service is to be containerized with Docker

### Prerequisites

- Install and start [Docker](https://store.docker.com/search?type=edition&offering=community)
- See keycloak's official [dockerhub](https://hub.docker.com/r/jboss/keycloak/) page for more details

### Blank Slate startup

1. For a "blank slate" deployment of keycloak, with in-memory H2 database, run below command : 
```shell
docker container run --name keycloak-local --rm --publish 8080:8080 -e KEYCLOAK_USER=admin -e KEYCLOAK_PASSWORD=p4ssw0rd -e DB_VENDOR=H2 jboss/keycloak
```
2. To access keycloak, go to http://localhost:8080 and select the `Administration Console`
3. Login with username `admin` and password `p4ssw0rd`
4. To stop keycloak, run below command :
```shell
docker container stop keycloak-local
```

### Pre-configured startup

1. For a deployment of keycloak using postgres with a pre-filled 'ThePugsRealm' Realm and a 'the-pugs-demo-app' Client, run below command with the file `realm-export.json` in the same folder :
```shell
docker network create keycloak-network
docker container run -d --rm --name postgres-live --net keycloak-network -e POSTGRES_DB=keycloak -e POSTGRES_USER=keycloak -e POSTGRES_PASSWORD=password postgres
docker container run --rm --name keycloak-live --net keycloak-network --publish 8080:8080 -e KEYCLOAK_USER=admin -e KEYCLOAK_PASSWORD=p4ssw0rd -e DB_VENDOR=postgres -e DB_ADDR=postgres-live -e KEYCLOAK_IMPORT=/tmp/realm-export.json -v $(pwd)/realm-export.json:/tmp/realm-export.json jboss/keycloak
```
2. Additional configurations needed in keycloak :
   - Go to http://localhost:8080 -> Admin Console -> log in as `admin`
   - Upper right corner -> user `The Pugs Inc` -> Manage Account
   - Add email and name
   - Go back to Admin console
   - In Realm Settings -> Email -> add password of `the.pugs.inc@gmail.com` (so keycloak can send verification emails to newly registered users)
3. To stop keycloak, run below command :
```shell
docker container stop keycloak-live
```

