# Kafka-cluster-docker-installation
Use docker compose to install the kafka cluster with UI tools: CMAK and Kafdrop

All the kafka command files are under /opt/kafka_2.12-2.4.1/bin

**Step1.** Execute the compose file

**Step2.** Access the CMAK Page http://localhost:9000 and add a new cluster

**Notice:**
**Cluster Zookeeper Hosts** should be the IP address that CMAK image can access inside the Docker.
Here is zookeeper:2181, but localhost:2181.

Otherwise, will get this issue

> Yikes! Ask timed out on [ActorSelection[Anchor(akka://kafka-manager-system/), Path(/user/kafka-manager)]] after [5000 ms]. Message of type [kafka.manager.model.ActorModel$KMAddCluster]. A typical reason for `AskTimeoutException` is that the recipient actor didn't send a reply.

**Step3**. Access the Kafdrop Page http://localhost:9001
