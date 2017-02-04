# Elasticsearch

This guide will show how to create a droplet on Digitalocean and start an Elasticsearch server.
We will run ES in a Docker container. The authentication will be handled by [HAProxy](https://github.com/pixerapp/haproxy).

## Create a Floating IP

```bash
doctl compute floating-ip create --region sgp1
```

## Assign a Floating IP to Domain Records

```bash
export FLOATING_IP=163.47.11.226

doctl compute domain records create --record-type A --record-name @ --record-data $FLOATING_IP $DOMAIN
doctl compute domain records create --record-type A --record-name www --record-data $FLOATING_IP $DOMAIN
doctl compute domain records create --record-type A --record-name cdn --record-data $FLOATING_IP $DOMAIN
doctl compute domain records create --record-type A --record-name api --record-data $FLOATING_IP $DOMAIN
doctl compute domain records create --record-type A --record-name es --record-data $FLOATING_IP $DOMAIN
```

## Create a Droplet via a Docker Machine

We will use a Docker Machine to create a droplet and install a Docker Engine in it.

Source: https://docs.docker.com/machine/drivers/digital-ocean/

```bash
docker-machine create \
  --driver digitalocean \
  --digitalocean-access-token token \
  --digitalocean-image ubuntu-16-04-x64 \
  --digitalocean-size 512mb \
  --digitalocean-region sgp1 \
  --digitalocean-ipv6 \
  --digitalocean-private-networking \
  --digitalocean-ssh-user root \
  --digitalocean-ssh-port 22 \
  --digitalocean-ssh-key-fingerprint asdf \
  pixerapp-es1
```

## Configure a Docker client.

Run the following command to configure the local `docker` client to work with the new remote Docker Engine.

    eval $(docker-machine env pixerapp-es1)

## Run Elasticsearch Server

We will run ES using a Docker Compose client.

    docker-compose up -d

It will start HAProxy with Elasticsearch.

## Access Elasticsearch

     curl https://pixerapp:password@es.pixerapp.com
