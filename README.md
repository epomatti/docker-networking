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