# Kafka-cluster-docker-installation
Use docker compose to install the kafka cluster with UI tools: CMAK and Kafdrop

**Step1.** Execute the compose file

**Step2.** Access the CMAK Page http://localhost:9000 and add a new cluster

**Notice:**
**Cluster Zookeeper Hosts** should be the IP address that CMAK image can access inside the Docker.
Here is zookeeper:2181, but localhost:2181.

**Step3**. Access the Kafdrop Page http://localhost:9001
