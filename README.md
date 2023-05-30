# Docker Networking

### Basic intro

```sh
# Create a network
docker network create -d bridge golden-gate
docker network inspect golden-gate

# Join the network
docker container run -dit --name sausalito --network golden-gate alpine sh
docker network inspect golden-gate

# Disconnect
docker network disconnect golden-gate sausalito

# Clean up
docker container rm sausalito -f
docker network rm golden-gate
```

### Single-host Bridge Networks

```sh
docker run -dit --name ctr1 alpine sh
docker run -dit --name ctr2 alpine sh
```

Connect to a container:

```sh
docker attach ctr1

$ ip addr show
$ ping -c 4 172.17.0.3
$ ping -c 4 ctr2 # DNS will not work
$ ping nigelpoulton.com
```

Create a new network:

```sh
docker network create --driver bridge ps-bridge
```

```sh
docker run -dit --name ctr3 --network ps-bridge alpine sh 
docker run -dit --name ctr4 --network ps-bridge alpine sh 
```

Test the DNS in this one:

```sh
docker attach ctr3
ping -c 4 ctr4 # DNS will work with embedded DNS server

ping -c 4 ctr1 # This won't, different bridge
```