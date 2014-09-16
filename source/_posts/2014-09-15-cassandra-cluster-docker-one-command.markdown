---
layout: post
title: "5-Node Cassandra Cluster. 1 Command."
date: 2014-09-15 00:16:56 -0400
comments: true
categories: docker cassandra cluster
---

[2014-09-16] Update: The command now brings up a 5-node Cassandra cluster in addition to DataStax OpsCenter 5.0.0 and wires it all up together. See [the GitHub repo](http://github.com/abh1nav/cassandra) for details. Each node is its own container that runs Cassandra and the DataStax Agent while OpsCenter is separted into its own container. 

[Original Post]

Run this command to bring up a 5-node Cassandra (2.1.0) cluster locally using Docker.
```
bash <(curl -sL http://bit.ly/docker-cassandra)
```
  
This will:  
1. Pull the `abh1nav/cassandra:latest` image.  
2. Start the first node with the name `cass1`  
3. Start `cass2..5` with the environment variable `SEED=<ip of cass1>`  
 
## Manual mode

If you don't like or trust the one liner, here's how to do it manually.

### Single Node Setup
To start the first node, pull the latest version of image:
```bash
docker pull abh1nav/cassandra:latest
```
  
Start the first instance:
```bash
docker run -d --name cass1 abh1nav/cassandra:latest
```
  
Grab its IP using:
  
{% codeblock lang:bash %}
{% raw %}SEED_IP=$(docker inspect -f '{{ .NetworkSettings.IPAddress }}' cass1){% endraw %}
{% endcodeblock %}
  
Connect to it using cqlsh:  
```bash
cqlsh $SEED_IP
```

The expected output is:
```
✈ megatron /opt/cassandra
 ↳ bin/cqlsh $SEED_IP
Connected to Test Cluster at 172.17.0.47:9160.
[cqlsh 4.1.1 | Cassandra 2.1.0 | CQL spec 3.1.1 | Thrift protocol 19.39.0]
Use HELP for help.
cqlsh>
```

### Cluster Setup
Once your single node is setup, you can add more nodes using:
```bash
for name in cass{2..5}; do
  echo "Starting node $name"
  docker run -d --name $name -e SEED=$SEED_IP abh1nav/cassandra:latest
  sleep 10
done
```
You can watch the cluster form by tailing the logs on `cass1`:
```
docker logs -f cass1
```
Once the cluster is up, you can check its status using:
```
nodetool --host $SEED_IP status
```
The expected output is:
```
✈ megatron /opt/cassandra
 ↳ bin/nodetool --host $SEED_IP status
Datacenter: datacenter1
=======================
Status=Up/Down
|/ State=Normal/Leaving/Joining/Moving
--  Address      Load       Tokens  Owns (effective)  Host ID                               Rack
UN  172.17.0.47  54.99 KB   256     37.3%             cb925207-ff79-4d1e-84ce-ac6c59353df4  rack1
UN  172.17.0.48  85.8 KB    256     39.4%             baa1b2c1-8f51-4e20-9c33-44cb5f45a4c0  rack1
UN  172.17.0.49  69.35 KB   256     40.1%             d1f96d59-c084-4ba3-a717-4269098cc854  rack1
UN  172.17.0.50  68.92 KB   256     40.2%             d514e844-e07a-4896-ace8-a0b43e25d6fc  rack1
UN  172.17.0.51  69.39 KB   256     43.0%             464cdf00-39e3-4efe-8a9f-83fc5ba839c9  rack1
```

Check out the [Docker registry page](https://registry.hub.docker.com/u/abh1nav/cassandra/) for the image and the [GitHub repo](https://github.com/abh1nav/cassandra) to grab the source.