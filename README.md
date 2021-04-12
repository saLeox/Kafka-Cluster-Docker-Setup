# Kafka-cluster-docker-installation & Experience
Use docker compose to install the kafka cluster with UI tools: CMAK and Kafdrop

## **Step1.** Prepare and execute the compose file

Ensure the JMX port of Kafka has been set properly in correspanding conifg, so that mornitoring tool can access internally.

      KAFKA_JMX_OPTS: "-Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false -Djava.rmi.server.hostname=kafka3 -Dcom.sun.management.jmxremote.rmi.port=1103"
      JMX_PORT: 1103

*hostname should be internal hostname, like kafka1, or kafka2, or kafka3*


## **Step2.** Configure and use CMAK

Access the CMAK Page at http://localhost:9000, and choose to add a  new cluster.

**Notice:**
**Cluster Zookeeper Hosts** should be the IP address that CMAK image can access inside the Docker. 

![](https://raw.githubusercontent.com/saLeox/photoHub/main/20210412162622.png)

Here is zookeeper:2181, but localhost:2181. Otherwise, will get this issue

> Yikes! Ask timed out on [ActorSelection[Anchor(akka://kafka-manager-system/), Path(/user/kafka-manager)]] after [5000 ms]. Message of type [kafka.manager.model.ActorModel$KMAddCluster]. A typical reason for `AskTimeoutException` is that the recipient actor didn't send a reply.


**Enable Metrics Mornitoring**
![](https://raw.githubusercontent.com/saLeox/photoHub/main/20210412164058.png)


**Useful features:**

**Metrics and consumer info**
![](https://raw.githubusercontent.com/saLeox/photoHub/main/20210412164239.png)

**Partitions distribution info**
![](https://raw.githubusercontent.com/saLeox/photoHub/main/20210412164827.png)


## **Step3**. Configure and use Kafdrop

In the compose file, set the kafka broker connect.

    KAFKA_BROKERCONNECT: "kafka1:29091"

It should link to internal endpoint, which is set in kafka part.

      KAFKA_LISTENERS: "INTERNAL://:29093,EXTERNAL://:9093"
      KAFKA_ADVERTISED_LISTENERS: "INTERNAL://kafka3:29093,EXTERNAL://localhost:9093"
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: "INTERNAL:PLAINTEXT,EXTERNAL:PLAINTEXT"
      KAFKA_INTER_BROKER_LISTENER_NAME: "INTERNAL"

At the same time, from outside, like producer or consumer, which need to access the bootstrap sever, need to ping external address.

**Access kafdrop at page http://localhost:9001**
View all necessary info in one single page.
![](https://raw.githubusercontent.com/saLeox/photoHub/main/20210412165805.png)

## Step 4. Kafka CLI

All the kafka command files are under /opt/kafka_2.12-2.4.1/bin

 1. **kafka-topics**

	Enter kafka-topics.sh can view all the options
	kafka-topics.sh --zookeeper zookeeper:2181 --list
	kafka-topics.sh --zookeeper zookeeper:2181 --create --topic first_topic --partitions 3 --replication-factor 2
	kafka-topics.sh --zookeeper zookeeper:2181 --describe --topic first_topic
	kafka-topics.sh --zookeeper zookeeper:2181 --delete --topic_first


 2. **kafka-console-producer**

	kafka-console-producer.sh --broker-list kafka1:9091, kafka2:9093 kafka3:9093--topic first_topic  --producer-property acks=all
	
> -   acks=0 The producer never waits for an ack from the broker when the ack value is set to 0. No guarantee can be made that the broker
> has received the message. The producer doesn’t try to send the record
> again since the producer never knows that the record was lost. This
> setting provides lower latency and higher throughput at the cost of
> much higher risk of message loss.
> -   acks=1 When setting the ack value to 1, the producer gets an ack after the leader has received the record. The leader will write the
> record to its log but will respond without awaiting a full
> acknowledgment from all followers. The message will be lost only if
> the leader fails immediately after acknowledging the record, but
> before the followers have replicated it. This setting is the middle
> ground for latency, throughput, and durability. It is slower but more
> durable than acks=0.
> -   acks=all Setting the ack value to all means that the producer gets an ack when all in-sync replicas have received the record. The leader
> will wait for the full set of in-sync replicas to acknowledge the
> record. This means that it takes a longer time to send a message with
> ack value all, but it gives the strongest message durability.




 3. **kafka-console-consumer**
 
	kafka-console-consumer.sh --bootstrap-server kafka3:9093 --topic first_topic --group app1  --from-beginning

## Insight about Kafka

![](https://raw.githubusercontent.com/saLeox/photoHub/main/20210412170807.png)

 - Broker and Bootstrap server in kafka consule CLI are broker in
   nature.
   
 - If there is no specific key given by producer, the broker will   
   distribute the request to random partition based on round robin   
   algorithm. Otherwise, it will send to the partition last request   
   went.

 - By rule, the number of consumer (group)s that can receive msg can be 
   less than the number of partitions.

 - Inside the consumer group, one consumer can consume the msg from more
   than one partitions. Msg from one partition can be only consumed by one consumer.
