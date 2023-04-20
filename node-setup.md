---
title: Setting up PulseChain node
---

This article assumes that `/blockchain` directory will be used to store all files created by the node client. It is also assumed that this directory will be one to one mapped as a volume into a Docker containers. You can replace this directory with any other location of your choice, however directory must be created with permissions allowing Docker to write there, before you proceed further.
{:.info}

# Types of nodes

PulseChain node software is composed of 2 client components:
- **Execution Layer** (EL) which can be:
  - **go-pulse** (**geth**) - Docker image `registry.gitlab.com/pulsechaincom/go-pulse`.
  - **Erigon** - Docker image `registry.gitlab.com/pulsechaincom/erigon-pulse`.
- **Consensus Layer** (CL) which can be:
  - **Prysm** - An implementation of the consensus protocol with a focus on usability, security, and reliability. Originally developed by Prysmatic Labs, a company with the sole focus on the development of their client.
    - Docker image `registry.gitlab.com/pulsechaincom/prysm-pulse/beacon-chain`.
  - **Lighthouse** - A consensus client with a heavy focus on speed and security. Originally built by Sigma Prime, an information security and software engineering firm who have funded Lighthouse along with the Ethereum Foundation, Consensys, and private individuals.
    - Docker image `registry.gitlab.com/pulsechaincom/lighthouse-pulse`.

You can combine any EL client and any CL client, but `geth` + `lighthouse` seems to be the easiest option. EL client will not work without CL.
{:.info}

In most cases you should use Docker image tagged as `latest`. By default Docker caches it's images therefore it's always good to run:
```sh
docker pull <registry-image-path>
```
to ensure you have the right Docker image content, before your run or start a Docker container with that image.

All mentioned Docker container are preconfigured to run specific node command and therefore any arguments added to the `docker run` command are passed as argument to the client command invoked within the container.

# Setting up RPC node

## Securing EL and CL communication

EL and CL clients need to communicate using _Engine API_ and for this purpose you need to create secret encryption key, so called _JWT Secret_. You can use standard `openssl` command line tool to do so:
```sh
openssl rand -hex 32 | tr -d "\n" > /blockchain/jwt.hex
```
(note that key must not contain the trailing newline).

Official PulseChain documentation recommends using Docker on "host" network. It is bit safer to create a virtual network for EL and CL clients' communication. You can do it by running:
```sh
docker network create pulsenet
```

## Running EL geth node

You can manually setup the `go-pulse` Docker container with PulseChain Execution Layer node by running:
```sh
docker run -d \
  --stop-timeout=180 \
  --restart always \
  --net=pulsenet \
  --name=pulsechain_execution \
  -p 127.0.0.1:8545:8545/tcp \
  -p 127.0.0.1:8546:8546/tcp \
  -p 30303:30303 \
  -v /blockchain:/blockchain \
  registry.gitlab.com/pulsechaincom/go-pulse:latest \
  --pulsechain-testnet-v4 \
  --datadir=/blockchain/geth \
  --authrpc.jwtsecret=/blockchain/jwt.hex \
  --authrpc.addr=0.0.0.0 \
  --authrpc.vhosts=pulsechain_execution \
  --ws \
  --ws.api=eth,net,web3 \
  --ws.addr=0.0.0.0 \
  --http \
  --http.api=eth,net,web3 \
  --http.addr=0.0.0.0
```

For node's P2P connectivity the TCP and UDP port 30303 should be made accessible from your network and also from the Internet.
{:.warning}

All the `--ws*` and `--http` switches, together with 8545 and 8546 port mappings can be removed, if you don't want to use your node as RPC service provider.
Note that RPC ports 8545 (HTTP) and 8646 (WebSockets) are only allowed locally on the host computer.
{:.info}

To see the logs of the container run:
```sh
docker logs -f pulsechain_execution
```
Here the optional `-f` flag allows follow mode for tracking new logged lines.

Typical messages and what they mean:
- `Post-merge network, but no beacon client seen. Please launch one to follow the chain!` - this means that no CL client connected to this EL client.

## Running EL Erigon node

You can manually setup the `erigon` Docker container with PulseChain Execution Layer node by running:
```sh
docker run -d \
  --stop-timeout=180 \
  --restart always \
  --net=pulsenet \
  --name=pulsechain_execution \
  -p 127.0.0.1:8545:8545/tcp \
  -p 30303:30303 \
  -p 30304:30304 \
  -p 42069:42609 \
  -v /blockchain:/blockchain \
  registry.gitlab.com/pulsechaincom/erigon-pulse:latest \
  --chain=pulsechain-testnet-v4 \
  --datadir=/blockchain/erigon \
  --authrpc.jwtsecret=/blockchain/jwt.hex \
  --authrpc.addr=0.0.0.0 \
  --authrpc.vhosts=pulsechain_execution \
  --externalcl \
  --snapshots=false
```

For node's P2P connectivity the TCP and UDP port 30303, 30304 and 42069 should be made accessible from your network and also from the Internet.
{:.warning}

Note that RPC ports 8545 (HTTP + WebSockets) are only allowed locally on the host computer.
{:.info}

To see the logs of the container run:
```sh
docker logs -f pulsechain_execution
```
Here the optional `-f` flag allows follow mode for tracking new logged lines.

## Running CL Lighthouse node

You can manually setup the `lighthouse-pulse` Docker container with PulseChain Consensus Layer node by running:
```sh
docker run -d \
  --stop-timeout=180 \
  --restart always \
  --net=pulsenet \
  --name=pulsechain_consensus \
  -p 9000:9000 \
  -v /blockchain:/blockchain \
  registry.gitlab.com/pulsechaincom/lighthouse-pulse \
  lighthouse \
  beacon_node \
  --network=pulsechain_testnet_v4 \
  --datadir=/blockchain/lighthouse \
  --execution-jwt=/blockchain/jwt.hex \
  --execution-endpoint=http://pulsechain_execution:8551 \
  --checkpoint-sync-url=https://checkpoint.v4.testnet.pulsechain.com \
  --http \
  --http-address 127.0.0.1
```

This command assumes that EL client exposes JWT interface on port 8551 (`pulsechain_execution` virtual address of it on the `pulsenet` virtual network).

For node's P2P connectivity the TCP and UDP port 9000 should be made accessible from your network and also from the Internet.
Note, that this Docker container is setup with `host` networking what makes it directly exposed to the network of the host where the container runs and the `-p` port mappings are ignored - they are stated to clarified which ports should be exposed in more general case.
{:.warning}

## Running CL Prysm node

You can manually setup the `prysm-pulse` Docker container with PulseChain Consensus Layer node by running:
```sh
docker run -d \
  --stop-timeout=180 \
  --restart always \
  --net=pulsenet \
  --name=pulsechain_consensus \
  -p 9000:9000 \
  -p 127.0.0.1:5052:5052 \
  -v /blockchain:/blockchain \
  registry.gitlab.com/pulsechaincom/prysm-pulse/beacon-chain \
  --pulsechain-testnet-v4 \
  --datadir=/blockchain/prysm \
  --jwt-secret=/blockchain/jwt.hex \
  --execution-endpoint=http://pulsechain_execution:8551 \
  --checkpoint-sync-url=https://checkpoint.v4.testnet.pulsechain.com \
  --genesis-beacon-api-url=https://checkpoint.v4.testnet.pulsechain.com
```

This command assumes that EL client exposes JWT interface on port 8551 (`pulsechain_execution` virtual address of it on the `pulsenet` virtual network).

For node's P2P connectivity the TCP and UDP port 9000 should be made accessible from your network and also from the Internet.
Note, that this Docker container is setup with `host` networking what makes it directly exposed to the network of the host where the container runs and the `-p` port mappings are ignored - they are stated to clarified which ports should be exposed in more general case.
{:.warning}

If you are behind firewall blocking incoming connections to port 9000 while supporting UPnP you can add `--enable-upnp`.
{:.info}

To see logs of this containers run:
```sh
docker logs -f pulsechain_consensus
```

## Docker Compose geth + Lighthouse

You can used `docker-compose` to setup and manage the Docker container. You will need a file named `docker-compose.yml` with the following content:
```yaml
version: '3.4'

services:
  pulsechain_execution:
    image: registry.gitlab.com/pulsechaincom/go-pulse:latest
    tty: true
    ports:
      - "127.0.0.1:8545:8545/tcp"
      - "127.0.0.1:8546:8546/tcp"
      - "127.0.0.1:6060:6060/tcp" # if used with --metrics or --pprof
      - "30303:30303"
    volumes:
      - /blockchain:/blockchain
    restart: unless-stopped
    stop_grace_period: 3m
    #oom_kill_disable: true
    command:
      - --datadir=/blockchain/execution
      - --pulsechain-testnet-v4
      - --authrpc.jwtsecret=/blockchain/secret/jwt.hex
      - --authrpc.addr=pulsechain_execution
      - --authrpc.vhosts=pulsechain_execution
      - --http
      - --http.api=eth,net,web3
      - --http.addr=0.0.0.0
      - --http.corsdomain=pulsechain_execution
      - --http.vhosts=pulsechain_execution
      - --ws
      - --ws.api=eth,net,web3
      - --ws.addr=0.0.0.0
      - --metrics
      - --metrics.addr=0.0.0.0 
      - --pprof
      - --pprof.addr=0.0.0.0
  pulsechain_consensus:
     image: registry.gitlab.com/pulsechaincom/lighthouse-pulse
     tty: true
     ports:
       - "9000:9000/tcp"
       - "9000:9000/udp"
       - "127.0.0.1:5052:5052/tcp"
     restart: unless-stopped
     depends_on:
       - pulsechain_execution
     volumes:
       - /blockchain:/blockchain
     command:
       - lighthouse
       - beacon_node
       - --network=pulsechain_testnet_v4
       - --datadir=/blockchain/consensus
       - --execution-jwt=/blockchain/secret/jwt.hex
       - --execution-endpoint=http://pulsechain_execution:8551
       - --checkpoint-sync-url=https://checkpoint.v4.testnet.pulsechain.com
       - --http
       - --http-address
       - 127.0.0.1
```
To start the container using this Docker Compose script you have to run the following command from within the directory where `docker-compose.yml` is located:
```sh
docker-compose up -d
```
The docker-compose tool will print names of container created or started. Note this name for any future actions with this (see below). If your current directory was let's say `pulsechain/` then the container will most likely be named `pulsechain_execution_1` and `pulsechain_consensus_1`.

To shutdown the container run from within the directory where `docker-compose.yml` is located:
```sh
docker-componse down
```

# Registering validator

To become a validator you must proceed with register at [PulseChain LaunchPad](https://launchpad.v4.testnet.pulsechain.com/en/overview).


docker run -it --rm -v /data/blockchain/pulsechain.secret:/blockchain/secret registry.gitlab.com/pulsechaincom/lighthouse-pulse:latest lighthouse --netwo
rk=pulsechain_testnet_v3 account validator import --directory=/blockchain/secret/validator_keys

docker run -it --rm -v /data/blockchain/pulsechain:/blockchain -v /data/blockchain/pulsechain.secret:/blockchain/secret registry.gitlab.com/pulsechaincom/lighthouse-pulse:latest lighthouse --network=pulsechain_testnet_v3 account validator import --directory=/blockchain/secret/validator_keys --datadir /blockchain/


