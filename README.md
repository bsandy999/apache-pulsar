## Apache-Pulsar Bare Metal Setup

The setup consists of:-

1.  [Zookeepers Node](https://pulsar.apache.org/docs/2.11.x/deploy-bare-metal/#deploy-a-zookeeper-cluster)
2.  [Cluster Metadata](https://pulsar.apache.org/docs/2.11.x/deploy-bare-metal/#initialize-cluster-metadata)
3.  [BookKeepers Node](https://pulsar.apache.org/docs/2.11.x/deploy-bare-metal/#deploy-a-bookkeeper-cluster)
4.  [Brokers Node](https://pulsar.apache.org/docs/2.11.x/deploy-bare-metal/#deploy-pulsar-brokers)
5.  [Client Service](https://pulsar.apache.org/docs/2.11.x/deploy-bare-metal/#connect-to-the-running-cluster)

---

[Zookeeper](https://zookeeper.apache.org/) manages a variety of essential coordination-related and configuration-related tasks for Pulsar. To deploy a Pulsar cluster, you need to deploy ZooKeeper first. A 3-node ZooKeeper cluster is the recommended configuration.

[Metadata](https://pulsar.apache.org/docs/2.11.x/deploy-bare-metal/#initialize-cluster-metadata) Once you deploy ZooKeeper for your cluster, you need to write some metadata to ZooKeeper. You only need to write this data once.

[BookKeepers](https://bookkeeper.apache.org/) handles all persistent data storage in Pulsar. You need to deploy a cluster of BookKeeper bookies to use Pulsar.

[Brokers](https://pulsar.apache.org/docs/2.11.x/admin-api-brokers/) handle Pulsar messages and provide the administrative interface of Pulsar.

[Client](https://pulsar.apache.org/docs/2.11.x/deploy-bare-metal/#connect-to-the-running-cluster) Once your Pulsar cluster is up and running, you should be able to connect with it using Pulsar clients. One such client is the `pulsar-client` tool, which is included with the Pulsar binary package. The pulsar-client tool can publish messages to and consume messages from Pulsar topics and thus provide a simple way to make sure that your cluster runs properly.

---

## Deployment

You can check out the [official site](https://pulsar.apache.org/docs/2.11.x/deploy-bare-metal/) for deployment steps/guides.

### Most commonly used commands

#### 1. Start zookeeper

```bash
cluster1/zk1/bin/pulsar-daemon start zookeeper
```

#### 2. Initialize cluster metadata <br>
##### Server 1 

```bash
cluster1/zk1/bin/pulsar initialize-cluster-metadata \
    --cluster c1 \
    --metadata-store zk:10.160.0.8:2181,10.160.0.9:2181,10.160.0.10:2181 \
    --configuration-metadata-store zk:10.160.0.8:2181,10.160.0.9:2181,10.160.0.10:2181 \
    --web-service-url http://10.160.0.8:8080,10.160.0.9:8080,10.160.0.10:8080 \
    --web-service-url-tls https://10.160.0.8:8443,10.160.0.9:8443,10.160.0.10:8443 \
    --broker-service-url pulsar://10.160.0.8:6650,10.160.0.9:6650,10.160.0.10:6650 \
    --broker-service-url-tls pulsar+ssl://10.160.0.8:6651,10.160.0.9:6651,10.160.0.10:6651
```
##### Server 2

```bash
cluster1/zk1/bin/pulsar initialize-cluster-metadata \
    --cluster c2 \
    --metadata-store zk:10.160.0.8:2181,10.160.0.9:2181,10.160.0.10:2181 \
    --configuration-metadata-store zk:10.160.0.8:2181,10.160.0.9:2181,10.160.0.10:2181 \
    --web-service-url http://10.160.0.8:8080,10.160.0.9:8080,10.160.0.10:8080 \
    --web-service-url-tls https://10.160.0.8:8443,10.160.0.9:8443,10.160.0.10:8443 \
    --broker-service-url pulsar://10.160.0.8:6650,10.160.0.9:6650,10.160.0.10:6650 \
    --broker-service-url-tls pulsar+ssl://10.160.0.8:6651,10.160.0.9:6651,10.160.0.10:6651
```
##### Server 3

```bash
cluster1/zk1/bin/pulsar initialize-cluster-metadata \
    --cluster c3 \
    --metadata-store zk:10.160.0.8:2181,10.160.0.9:2181,10.160.0.10:2181 \
    --configuration-metadata-store zk:10.160.0.8:2181,10.160.0.9:2181,10.160.0.10:2181 \
    --web-service-url http://10.160.0.8:8080,10.160.0.9:8080,10.160.0.10:8080 \
    --web-service-url-tls https://10.160.0.8:8443,10.160.0.9:8443,10.160.0.10:8443 \
    --broker-service-url pulsar://10.160.0.8:6650,10.160.0.9:6650,10.160.0.10:6650 \
    --broker-service-url-tls pulsar+ssl://10.160.0.8:6651,10.160.0.9:6651,10.160.0.10:6651
```
