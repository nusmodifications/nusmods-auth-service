# NUSMods Authentication Service

NUSMods' auth service is a Dockerized, self-hosted instance of the
[Keycloak](https://www.keycloak.org) Identity and Access Manangement Solution
deployed to Dokku. It is based off Keycloak's official docker image with some
slight modifications. This repository was originally forked from
https://github.com/mieckert/keycloak-heroku, which adapted Keycloak to work
with Heroku.

## Deployment

This section contains instructions to deploy this service to NUSMods
production servers.

### First-time installation

1. On server: Install Dokku
1. At this point, you may have to whitelist an SSH key with Dokku.
1. On server: `dokku domains:set-global 2.nusmods.com`
1. On server: `sudo dokku plugin:install https://github.com/dokku/dokku-postgres.git`
1. On server: `sudo dokku plugin:install https://github.com/dokku/dokku-letsencrypt.git`
1. On server: `dokku apps:create keycloak`
1. On server: `dokku postgres:create keycloakdb`
1. On server: `dokku postgres:link keycloakdb keycloak`
1. On your machine: `git clone https://github.com/nusmodifications/nusmods-auth-service.git`
1. On your machine: `cd nusmods-auth-service`
1. On your machine: `git remote add dokku dokku@nusmods2:keycloak`
1. On your machine: `git push dokku master`
1. On server: `dokku proxy:ports-set keycloak http:80:8080`
1. On server: `dokku letsencrypt keycloak`
1. On server: `dokku config:set keycloak KEYCLOAK_USER=<admin user> KEYCLOAK_PASSWORD=<admin password> PROXY_ADDRESS_FORWARDING=true` (not sure if PAF is neccesary)
1. On server: `dokku proxy:ports-set keycloak http:80:8080 https:443:8080` (don't use Keycloak's port 8443 as it will try to do HTTPS itself instead of letting our reverse proxy do it for us)

### Upgrading

On your machine:

1. `cd nusmods-auth-service`
1. `git pull`
1. Update the `jboss/keycloak` image version in Dockerfile and commit the changes.
1. Deploy: `git push dokku master`
1. Update the origin repo: `git push origin master`
