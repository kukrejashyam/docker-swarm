# Docker Swarm Lab - MongoDB & Mongo Express

This lab demonstrates:

- Docker Swarm Initialization
- Manager & Worker Node Architecture
- Overlay Networking
- Service Replication
- Placement Constraints
- Docker Stack Deployment
- Service Scaling in Docker Swarm

---

# Prerequisites

- 2 Linux EC2 Instances / Virtual Machines
- Docker Installed on both machines
- One node should act as:
  - Manager Node
  - Worker Node

---

# Lab Architecture

| Service | Replicas | Runs On |
|----------|-----------|----------|
| MongoDB | 1 | Worker Node |
| Mongo Express | 3 | Worker Node |

---

# Important - Open Required Security Group Ports

Before starting Docker Swarm setup, ensure the following ports are allowed between Manager and Worker nodes.

---

# MANAGER NODE - Inbound Rules

| Port | Protocol | Purpose |
|------|-----------|----------|
| 2377 | TCP | Swarm Cluster Management |
| 7946 | TCP/UDP | Node Communication |
| 4789 | UDP | Overlay Network Traffic |
| 8081 | TCP | Mongo Express UI Access |
| 22 | TCP | SSH Access |

---

# WORKER NODE - Inbound Rules

| Port | Protocol | Purpose |
|------|-----------|----------|
| 7946 | TCP/UDP | Node Communication |
| 4789 | UDP | Overlay Network Traffic |
| 8081 | TCP | Mongo Express UI Access |
| 27017 | TCP | MongoDB Access |
| 22 | TCP | SSH Access |

---

# Recommended Security Group Source

For lab/demo purposes:

- Allow traffic from the same Security Group
OR
- Allow traffic from Manager/Worker private IP range

Example:

```text
172.31.0.0/16
```

---

# Important Ports Explained

## Port 2377
Used by Worker Nodes to join the Swarm cluster.

## Port 7946
Used for communication between Docker nodes.

## Port 4789
Used for Overlay Networking traffic between containers across nodes.

---

# Step 1 - Clone Repository

## Run ONLY on MANAGER NODE

```bash
git clone <YOUR_GITHUB_REPO_URL>
cd docker-swarm
```

Example:

```bash
git clone https://github.com/kukrejashyam/docker-swarm.git
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

# Step 5 - Clone Repository on Manager Node

## Run ONLY on MANAGER NODE

```bash
git clone <YOUR_GITHUB_REPO_URL>
cd docker-swarm
```

---

# Step 6 - Deploy Docker Stack

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

# Step 7 - Verify Services

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

# Step 8 - Verify Service Placement

## Run ONLY on MANAGER NODE

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

# Step 9 - Verify Running Containers

## Run ONLY on WORKER NODE

```bash
docker ps
```

Expected Observation:
- MongoDB Container Running
- Multiple Mongo Express Containers Running

---

# Step 10 - Access Mongo Express UI

Open browser:

```bash
http://<WORKER_NODE_PUBLIC_IP>:8081
```

Login Credentials:

| Username | Password |
|----------|----------|
| admin | password |

---

# Step 11 - Scale Services

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

# Step 12 - Verify Scaling

## Run ONLY on WORKER NODE

```bash
docker ps
```

Observe:
- Additional Mongo Express containers created automatically

---

# Step 13 - Inspect Overlay Network

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

# Step 14 - Remove Stack

## Run ONLY on MANAGER NODE

```bash
docker stack rm mongo-stack
```

---

# Step 15 - Leave Swarm (Optional Cleanup)

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

## Worker Node Unable to Join Swarm

Check:
- Port 2377 open on Manager Node
- Security Group configured correctly
- Manager private IP reachable from Worker

---

## Service Stuck at 0 Replicas

Check:
- Worker node joined successfully
- Docker daemon running
- Overlay network created properly

---

## Mongo Express Not Opening

Check:
- Port 8081 allowed in Security Group
- Service is running
- Correct public IP used

---

# Learning Outcomes

By completing this lab, students will understand:

- Docker Swarm Cluster Setup
- Multi-node Container Orchestration
- Service Replication
- Docker Stack Deployment
- Overlay Networking
- Placement Constraints
- Scaling Applications in Swarm
- Manager vs Worker Node Responsibilities

---
