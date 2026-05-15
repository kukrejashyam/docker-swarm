# Docker Swarm Lab - MongoDB & Mongo Express

This lab demonstrates:

- Docker Swarm Initialization
- Manager & Worker Node Architecture
- Overlay Networking
- Service Replication
- Container Placement Constraints
- Docker Stack Deployment
- Scaling Services in Swarm

---

# Prerequisites

- 2 Linux EC2 Instances / Virtual Machines
- Docker Installed on both machines
- One node should act as:
  - Manager Node
  - Worker Node

---

# Architecture

| Service | Replicas | Runs On |
|----------|-----------|----------|
| MongoDB | 1 | Worker Node |
| Mongo Express | 3 | Worker Node |

---

# Step 1 - Clone Repository

## Run ONLY on MANAGER NODE

```bash
git clone <YOUR_GITHUB_REPO_URL>
cd docker-swarm
```

Example:

```bash
git clone https://github.com/yourusername/docker-swarm.git
cd docker-swarm
```

---

# Step 2 - Initialize Docker Swarm

## Run ONLY on MANAGER NODE

```bash
docker swarm init
```

Expected Output:

```bash
docker swarm join --token SWMTKN-xxxxxx <MANAGER-IP>:2377
```

Copy the complete join command.

---

# Step 3 - Join Worker Node to Swarm

## Run ONLY on WORKER NODE

Paste the join command copied earlier.

Example:

```bash
docker swarm join --token SWMTKN-xxxxxx 172.x.x.x:2377
```

Expected Output:

```bash
This node joined a swarm as a worker.
```

---

# Step 4 - Verify Swarm Nodes

## Run ONLY on MANAGER NODE

```bash
docker node ls
```

Expected Output:

```bash
ID                            HOSTNAME       STATUS    AVAILABILITY   MANAGER STATUS
xxxxxx                        manager-node   Ready     Active         Leader
yyyyyy                        worker-node    Ready     Active
```

---

# Step 5 - Deploy Docker Stack

## Run ONLY on MANAGER NODE

```bash
docker stack deploy -c docker-compose.yaml mongo-stack
```

Expected Output:

```bash
Creating network mongo-stack_mongo-network
Creating service mongo-stack_mongodb
Creating service mongo-stack_mongo-express
```

---

# Step 6 - Verify Services

## Run ONLY on MANAGER NODE

```bash
docker service ls
```

Expected Output:

```bash
ID              NAME                          MODE        REPLICAS
xxxxx           mongo-stack_mongodb           replicated  1/1
xxxxx           mongo-stack_mongo-express     replicated  3/3
```

---

# Step 7 - Verify Containers Running on Worker Node

## Run on MANAGER NODE

```bash
docker service ps mongo-stack_mongodb
```

```bash
docker service ps mongo-stack_mongo-express
```

Observe:
- Containers are deployed only on Worker Node
- Placement constraints are working correctly

---

# Step 8 - Verify Running Containers

## Run ONLY on WORKER NODE

```bash
docker ps
```

Expected Observation:
- MongoDB Container Running
- Multiple Mongo Express Containers Running

---

# Step 9 - Access Mongo Express UI

## Open Browser

```bash
http://<WORKER_NODE_IP>:8081
```

Login Credentials:

| Username | Password |
|----------|----------|
| admin | password |

---

# Step 10 - Scale Services

## Run ONLY on MANAGER NODE

Scale Mongo Express to 5 replicas:

```bash
docker service scale mongo-stack_mongo-express=5
```

Verify:

```bash
docker service ls
```

Expected Output:

```bash
mongo-stack_mongo-express   replicated   5/5
```

---

# Step 11 - Verify Scaling

## Run ONLY on WORKER NODE

```bash
docker ps
```

Observe:
- Additional Mongo Express containers created automatically

---

# Step 12 - Inspect Overlay Network

## Run ONLY on MANAGER NODE

List networks:

```bash
docker network ls
```

Inspect overlay network:

```bash
docker network inspect mongo-stack_mongo-network
```

Observe:
- Overlay Driver
- Connected Containers
- Internal Swarm Networking

---

# Step 13 - Remove Stack

## Run ONLY on MANAGER NODE

```bash
docker stack rm mongo-stack
```

---

# Step 14 - Leave Swarm (Optional Cleanup)

## Run ONLY on WORKER NODE

```bash
docker swarm leave
```

---

## Run ONLY on MANAGER NODE

```bash
docker swarm leave --force
```

---

# Important Concepts Demonstrated

## Docker Swarm Features
- Swarm Manager
- Worker Nodes
- Desired State
- Self Healing
- Replication
- Load Balancing
- Overlay Networking

---

# Placement Constraint Used

```yaml
placement:
  constraints:
    - node.role == worker
```

This ensures containers run only on worker nodes.

---

# Useful Commands

# MANAGER NODE Commands

## View Services

```bash
docker service ls
```

## View Tasks

```bash
docker service ps <service-name>
```

Example:

```bash
docker service ps mongo-stack_mongo-express
```

## View Nodes

```bash
docker node ls
```

## Inspect Stack

```bash
docker stack services mongo-stack
```

---

# WORKER NODE Commands

## View Running Containers

```bash
docker ps
```

## View Container Logs

```bash
docker logs <container-id>
```

---

# Troubleshooting

## Service Stuck at 0 Replicas

Check:
- Worker node joined successfully
- Docker daemon running
- Overlay network created properly

---

## Mongo Express Not Opening

Check:
- Port 8081 allowed in Security Group / Firewall
- Service is running
- Worker node IP is correct

---

# Learning Outcome

By completing this lab, students will understand:

- Docker Swarm Cluster Setup
- Multi-node Container Orchestration
- Service Replication
- Stack Deployment
- Overlay Networking
- Placement Constraints
- Scaling Applications in Swarm

---
