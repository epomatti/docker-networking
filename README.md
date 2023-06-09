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

You can connect a container to multiple networks:

```sh
docker network connect ps-bridge ctr1
```

Publishing an app:

```sh
docker container run -dit \
  --name web \
  --network ps-bridge \
  --publish 5000:8080 \
  nigelpoulton/pluralsight-docker-ci
```

## Service Discovery

```sh
# Attachable overlay
docker network create --driver overlay --attachable marvel
```

```sh
docker service create --name shield \
  --network marvel \
  --replicas 3 \
  nigelpoulton/k8sbook:shield01
```

```sh
docker service ls
```

```
docker network inspect marvel
```

Virtual IP (IP tables, IPVS) - load balancing


cat /etc/resolv.conf