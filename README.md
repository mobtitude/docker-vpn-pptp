# VPN (PPTP) for Docker

This is a docker image with simple VPN (PPTP) server with _chap-secrets_ authentication.

PPTP uses _/etc/ppp/chap-secrets_ file to authenticate VPN users.
You need to create this file on your own and link it to docker when starting a container.

Example of _chap-secrets_ file:

````
# Secrets for authentication using PAP
# client    server      secret      acceptable local IP addresses
username    *           password    *
````


## Starting VPN server

To start VPN server as a docker container run:

````
docker run -d --privileged -p 1723:1723 -v {local_path_to_chap_secrets}:/etc/ppp/chap-secrets mobtitude/vpn-pptp
````

Edit your local _chap-secrets_ file, to add or modify VPN users whenever you need.
When adding new users to _chap-secrets_ file, you don't need to restart Docker container.

## Connecting to VPN service
You can use any VPN (PPTP) client to connect to the service.
To authenticate use credentials provided in _chap-secrets_ file.


## Troubleshooting 

### Docker 1.7.x and connection issues
After upgrading from Docker 1.3.0 to Docker 1.7.1 the containers started from image `mobtitude/vpn-pptp` stopped accepting connections to VPN without any reason.
Connections were dropped after timeout. 

It looked like Docker deamon didn't forward packets for GRE protocol to container.

One of the possible solutions is to start container with networking mode set to host by adding param `--net=host` to run command:

````
docker run -d --privileged --net=host -v {local_path_to_chap_secrets}:/etc/ppp/chap-secrets mobtitude/vpn-pptp
````

**Note:** Before starting container in `--net=host` mode, please read how networking in `host` mode works in Docker:
https://docs.docker.com/reference/run/#mode-host

