---
title: Setting up PulseChain node
---

This article assumes that `/blockchain` directory will be used to store all files created by the node. It is also assumed that this directory will be one to one mapped as a volume into a Docker container. Of course both these directories can be changed.
{:.info}

# Types of nodes

PulseChain node software (`geth`) is available as a Docker image and can be pulled from [registry.gitlab.com/pulsechaincom/go-pulse](registry.gitlab.com/pulsechaincom/go-pulse). Typically the latest version shall be used.
Docker container is preconfigured to run `geth` command and therefore any arguments added to the `docker run` command are passed as argument to the `geth` invoked within the container.

You can run in the following mode:
- **Validator node** - this is a validating node of the consensus part of the blockchain network. Validator node has to have an associated account address and must be running in `--mine` mode which allows for sealing of new block. Validator has to be registered (see below) into the PulseChain smart contract to become a candidate for next rotation.
- **Standard node** - this is standard non-archive node of the blockchain network. At the moment (version 0.8.0) this is technically equivalent to a validator mode, because some of the transactions required `--mine` mode to be enabled and this also required an associated account to be unlocked.
- **Full "archive" node** - this is same as before, node uses `--syncmode full` synchronisation mode.

# Setting up Validator node

## Creating a private key
Validator must have a Ethereum-like address and associated private key. Simplest way to obtain the key is to create it using `geth`:

```sh
docker run -v /blockchain:/blockchain --rm -it \
        registry.gitlab.com/pulsechaincom/go-pulse \
        account new --datadir=/blockchain
```

This command creates a temporary Docker container and runs under it the command `geth account new` using `/blockchain` as the data directory. You will be prompted to enter password for storing the generated private key.
Along with other lines this command should print:
```
Public address of the key:   0xADDRESS...
Path of the secret key file: /blockchain/keystore/...
```
Remember the 0xADDRESS here. You shall backup your private key stored in `/blockchain/keystore`.

## Using existing private key 

You can use externally generated account (private key) as well. To do so, store the private key into a file under the `/blockchain` directory for example as `/blockchain/privatekey.txt`. The content of the file should be a private key encoded as hexadecimal string (not seed words) without the leading `0x` characters.

```sh
docker run -v /blockchain:/blockchain --rm -it \
        registry.gitlab.com/pulsechaincom/go-pulse \
        account import --datadir=/blockchain /blockchain/privatekey.txt
```
This command creates a temporary Docker container and runs under it the command `geth account import` using `/blockchain` as the data directory.
When the key import succeeds you can remove `/blockchain/privatekey.txt`.

## Running node as a Docker container

You can setup the Docker container with PulseChain node manually. You have to have a file with the password for the private key stored under the `/blockchain` directory for example as `/blockchain/password.txt`.
```sh
docker run -d -v /blockchain:/blockchain \
	--oom-kill-disable --restart always --stop-timeout=180 \
	-p 30312:30312/tcp -p 30312:30312/udp \
	--name pulsechain_node \
	registry.gitlab.com/pulsechaincom/go-pulse \
	--datadir=/blockchain --pulsechain-testnet \
	--mine --allow-insecure-unlock --unlock 0xADDRESS --password /blockchain/password.txt
```
<!-- TODO: what ports are needed for validator node? -->
 
To see the logs of the container run:
```sh
docker logs -f pulsechain_node
```
Here the optional `-f` flag allows follow mode for tracking new logged lines.

## Docker Compose script

You can used `docker-compose` to setup and manage the Docker container. You will need a file named `docker-compose.yml` with the following content:
```yaml
version: '3.4'

services:
  node:
    image: registry.gitlab.com/pulsechaincom/go-pulse # use latest image
    tty: true # enable fancier color output
    ports:
      - "8575:8575/tcp"
      - "8576:8576/tcp"
      - "30312:30312/tcp"
      - "30312:30312/udp"
    volumes:
      - /blockchain:/blockchain
    restart: always
    stop_grace_period: 3m
    oom_kill_disable: true
    command:
      - --datadir=/blockchain
      - --pulsechain-testnet
      - --unlock=0xADDRESS
      - --allow-insecure-unlock
      - --password=/blockchain/pass.txt
      - --mine
```
To start the container using this Docker Compose script you have to run the following command from within the directory where `docker-compose.yml` is located:
```sh
docker-compose up -d
```
The docker-compose tool will print names of container created or started. Note this name for any future actions with this (see below). If your current directory was let's say `pulsechain/` then the container will most likely be named `pulsechain_node_1`.

To shutdown the container run from within the directory where `docker-compose.yml` is located:
```sh
docker-componse down
```

# Sanity checks

Before proceeding with further it is recommended to ensure:
- Your hosting system can reboot, check that node container automatically starts should this happen.
- Your firewall allows external incoming connections to port 30312 (TCP and UDP) to the container. Though this is not strictly required for running any type of node, 

# Registering validator

Once you have validator node technically running you can register your validator trough the [Validators & Staking user interface](index#validators--staking-dapp).
Validator registration requires *creating a nonrefundable deposit* (at the moment it is 500000 PLS). You validator should automatically activate in the next rotation if there is enough staked PLS delegated to your address.

# Planning downtimes

Validator nodes are expected to run 24x7 without any downtime at all. **When the node is in rotation**, it will be penalised slashing for any downtime. If any maintenance is necessary you can:
- In case of short maintenance (e.g. container or host system restart, container migration) which is expected to take up to several minutes, you can simply let the node to be slashed. Slashes will be automatically removed during the next rotation when your node will assumable run properly.
- For a longer downtime you should unregister the validator using the [Validators & Staking user interface](index#validators--staking-dapp), this will remove the node from the current rotation and you can do any maintenance. You can then register the validator again and your node will eligible to become validator into next rotation. Note that meanwhile some of the stakers can remove their stakes.

# Setting up RPC node

RPC node is intended to provide PulseChain (Ethereum compatible) API for external blockchain applications. Both RPC (HTTP) or WebSocket (JSON-RPC) interface are available.
At the moment running the RPC node requires the `--mine` argument, otherwise some types of transactions would fail. Therefore you have to have a private key imported (as for validator node [above](#creating-a-private-key)) and also file with the password for the private key stored under the `/blockchain` directory for example as `/blockchain/password.txt`.

You can setup the Docker container with PulseChain RPC node manually using this command:
```sh
docker run -d -v /blockchain:/blockchain \
        --oom-kill-disable --restart always --stop-timeout=180 \
	-p 30312:30312/tcp -p 30312:30312/udp -p 8575:8575/tcp -p 8576:8576/tcp \
	--name pulsechain_rpcnode \
	registry.gitlab.com/pulsechaincom/go-pulse \
	--datadir=/blockchain --pulsechain-testnet \
	--http --http.api eth,net,web3 --http.addr 0.0.0.0 --http.corsdomain '*' \
	--ws --ws.api eth,net,web3 --ws.addr 0.0.0.0 --ws.origins '*' \
	--mine --allow-insecure-unlock --unlock 0xADDRESS --password=/blockchain/password.txt \
	--syncmode full
```
Last line `--syncmode full` is optional and it's needed only for running an archive node.

# Add node to Dashboard

There is a community ran [Pulse Chain Status Dashboard](https://plsnetstats.com/) which display status of several PulseChain nodes. In order to register your node(s), you need need to operated `eth-net-intelligence-api` service which talks to your PulseChain node through the RPC web service interface (typically TCP port 8575).

If you configured the validator node using instructions above you need add arguments `--http --http.api=eth,net,web3 --http.addr=0.0.0.0` to be passed to `geth`.
{:.warning}

Create a working directory (name of the directory will be used to form name of Docker container created by the `docker-compose` tool) for example named `stats`. In this directory create a file named `Dockerfile` with the following content:
```Dockerfile
FROM node

RUN apt-get update && \
        apt-get install git && \
        apt-get clean

RUN git clone https://github.com/ethereum/eth-net-intelligence-api && \
        cd eth-net-intelligence-api && \
        npm install

WORKDIR eth-net-intelligence-api

ENTRYPOINT node app.js
```
All further commands have to be executed from within the working directory.

Now build Docker image with `eth-net-intelligence-api` tool by running:
```sh
docker build -t eth-net-intelligence-api .
```

As the next test create file `docker-compose.yml` with the following content:
```yaml
version: '3.4'

services:
  monitor:
    image: eth-net-intelligence-api
    tty: true
    restart: unless-stopped
    network_mode: "host"
    environment:
      INSTANCE_NAME: <Human Readable Name of Your Node>
      WS_SERVER: wss://plsnetstats.com
      RPC_HOST: localhost
      RPC_PORT: 8575
      WS_SECRET: <secret-password>
```

The value for `WS_SECRET` can be obtained from the [@PulseDEV Telegram group](https://t.me/PulseDEV).

You need to make sure that Docker networking is setup so that `node` processed running under the `monitor` container can access the RPC port of the PulseChain node. You can considering merging both `docker-compose.yml` files into one and configured shared networking - then you need to modify the `RPC_HOST` value to address the PulseChain node.
{:.info}

Finally register the container by running:
```sh
docker-compose up -d
```

