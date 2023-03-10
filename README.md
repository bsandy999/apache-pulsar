## Apache-Pulsar Bare Metal Setup

The setup consists of:-

1.  [Zookeepers Node](https://pulsar.apache.org/docs/2.11.x/deploy-bare-metal/#deploy-a-zookeeper-cluster)
2.  [Cluster Metadata](https://pulsar.apache.org/docs/2.11.x/deploy-bare-metal/#initialize-cluster-metadata)
3.  [BookKeepers Node](https://pulsar.apache.org/docs/2.11.x/deploy-bare-metal/#deploy-a-bookkeeper-cluster)
4.  [Brokers Node](https://pulsar.apache.org/docs/2.11.x/deploy-bare-metal/#deploy-pulsar-brokers)
5.  [Client Service](https://pulsar.apache.org/docs/2.11.x/deploy-bare-metal/#connect-to-the-running-cluster)

---

- [Zookeeper](https://zookeeper.apache.org/) manages a variety of essential coordination-related and configuration-related tasks for Pulsar. To deploy a Pulsar cluster, you need to deploy ZooKeeper first. A 3-node ZooKeeper cluster is the recommended configuration.

- [Metadata](https://pulsar.apache.org/docs/2.11.x/deploy-bare-metal/#initialize-cluster-metadata) Once you deploy ZooKeeper for your cluster, you need to write some metadata to ZooKeeper. You only need to write this data once.

- [BookKeepers](https://bookkeeper.apache.org/) handles all persistent data storage in Pulsar. You need to deploy a cluster of BookKeeper bookies to use Pulsar.

- [Brokers](https://pulsar.apache.org/docs/2.11.x/admin-api-brokers/) handle Pulsar messages and provide the administrative interface of Pulsar.

- [Client](https://pulsar.apache.org/docs/2.11.x/deploy-bare-metal/#connect-to-the-running-cluster) Once your Pulsar cluster is up and running, you should be able to connect with it using Pulsar clients. One such client is the `pulsar-client` tool, which is included with the Pulsar binary package. The pulsar-client tool can publish messages to and consume messages from Pulsar topics and thus provide a simple way to make sure that your cluster runs properly.

---

## Deployment

You can check out the [official site](https://pulsar.apache.org/docs/2.11.x/deploy-bare-metal/) for deployment steps/guides.

##### Most commonly used commands

#### 1. Start zookeeper

```bash
cluster1/zk1/bin/pulsar-daemon start zookeeper
```

#### 2. Initialize cluster metadata <br>
##### Server 1 

```bash
cluster1/zk1/bin/pulsar initialize-cluster-metadata \
    --cluster node1 \
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
    --cluster node2 \
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
    --cluster node3 \
    --metadata-store zk:10.160.0.8:2181,10.160.0.9:2181,10.160.0.10:2181 \
    --configuration-metadata-store zk:10.160.0.8:2181,10.160.0.9:2181,10.160.0.10:2181 \
    --web-service-url http://10.160.0.8:8080,10.160.0.9:8080,10.160.0.10:8080 \
    --web-service-url-tls https://10.160.0.8:8443,10.160.0.9:8443,10.160.0.10:8443 \
    --broker-service-url pulsar://10.160.0.8:6650,10.160.0.9:6650,10.160.0.10:6650 \
    --broker-service-url-tls pulsar+ssl://10.160.0.8:6651,10.160.0.9:6651,10.160.0.10:6651
```
#### 3. Deploy a BookKeeper cluster
```bash
bk-cluster/bk1/bin/pulsar-daemon start bookie
bk-cluster/bk2/bin/pulsar-daemon start bookie
bk-cluster/bk3/bin/pulsar-daemon start bookie
```
On successful bookie start up your Prometheus monitoring dashboard must look like this without any error

![image](https://user-images.githubusercontent.com/3489735/216020985-72877bdb-77a6-458c-b7c7-51ce2f55a1ea.png)

You can verify that a bookie works properly by running the `bookiesanity` command on the BookKeeper shell:

```
bk-cluster/bk1/bin/bookkeeper shell bookiesanity
```

After you start all the bookies, you can use `simpletest` command for BookKeeper shell on any bookie node, to verify all the bookies in the cluster are up running.
```
bk-cluster/bk1/bin/bookkeeper shell simpletest --ensemble <num-bookies> --writeQuorum <num-bookies> --ackQuorum <num-bookies> --numEntries <num-entries>
```

#### 4. Deploy Pulsar brokers
```
cluster1/broker1/bin/pulsar-daemon start broker
```

#### 5. Connect to the running cluster
for detailed information please follow the steps [link](https://pulsar.apache.org/docs/2.11.x/deploy-bare-metal/#connect-to-the-running-cluster)


## Pulsar admin API interfaces
#### 1. List Clusters
```
cluster1/broker1/bin/pulsar-admin clusters list
```
#### 2. Tenant resources
``` 
cluster1/broker1/bin/pulsar-admin tenants list
```
#### 3. Brokers resources
```
cluster1/broker1/bin/pulsar-admin brokers list use 
cluster1/broker1/bin/pulsar-admin brokers leader-broker   //get leader broker
```
Once that is complete, you can publish a message to the Pulsar topic:

```
cluster1/broker1/bin/pulsar-client produce \
    persistent://public/default/test \
    -n 1 \
    -m "Hello Pulsar"

```
This command publishes a single message to the Pulsar topic. In addition, you can subscribe to the Pulsar topic in a different terminal before publishing messages as below:

```
cluster1/broker1/bin/pulsar-client consume \
    persistent://public/default/test \
    -n 100 \
    -s "consumer-test" \
    -t "Exclusive"

```
#### 4. Managing Namespaces
```
cluster1/broker1/bin/pulsar-admin namespaces list test-tenant
cluster1/broker1/bin/pulsar-admin namespaces create test-tenant/namespace1   
cluster1/broker1/bin/pulsar-admin namespaces policies test-tenant/namespace1  //fetch the current policies associated with a namespace
cluster1/broker1/bin/pulsar-admin namespaces delete test-tenant/namespace1
```
 ###### 4.1  Configure replication clusters
 ```
 cluster1/broker1/bin/pulsar-admin namespaces set-clusters test-tenant/namespace1 --clusters cl1,cl2
 cluster1/broker1/bin/pulsar-admin namespaces get-clusters test-tenant/cluster1/namespace1
 ```
  ###### 4.2  Managing permissions
```
cluster1/broker1/bin/pulsar-admin namespaces grant-permission cpaas/email \
    --actions produce,consume \
    --role admin
    
cluster1/broker1/bin/pulsar-admin namespaces permissions cpaas/email
```

#### 5. Manage topics
```
cluster1/broker1/bin/pulsar-admin topics list my-tenant/my-namespace
cluster1/broker1/bin/pulsar-admin topics create-partitioned-topic -p 9 persistent://my-tenant/my-namespace/topic
cluster1/broker1/bin/pulsar-admin persistent get-partitioned-topic-metadata persistent://my-tenant/my-namespace/topic
cluster1/broker1/bin/pulsar-admin persistent partitioned-stats persistent://my-tenant/my-namespace/topic --per-partition
cluster1/broker1/bin/pulsar-admin topics partitioned-lookup persistent://my-tenant/my-namespace/topic --sort-by-broker
cluster1/broker1/bin/pulsar-admin persistent delete-partitioned-topic persistent://my-tenant/my-namespace/topic
for i in $(seq 0 8); do cluster1/broker1/bin/pulsar-admin topics lookup persistent://my-tenant/my-namespace/topic-$i; done

```
