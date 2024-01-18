---
title: "Scala 3 with ScyllaDB"
summary: "ScyllaDB with Scala 3 as an alternative to DDB."
date: 2024-01-17T17:36:48-05:00
draft: false
---

## Background and motivation

I'm not going to beat around the bush in this article. I had the misfortune to work with DynamoDB last year. It was absolutely terrible.

If you're a DDB fan, good for you. I hope it works well. I mean that. But I found the experience absolutely attrocious. Summarily, my main (but not complete) complaints are:

- DDB doesn't give you fine-grained control for optimizations.
- OnDemand scaling doesn't work magically, and the way it works is hard to predict. Sure, there are some rules spelled out in the AWS docs around doubling previous read/write peaks for next scaling level, etc... but there are limitations around how *quickly* a table will scale.
- This one is less specific to DDB, but more specific to AWS technologies in general - if you hit a write throughput throttle, you *are* going to lose your data. Yes, you can argue that AWS has to protect itself by setting limits, and I will respond by pointing out that I'm actually not a fan of "cloud computing" anyway, and given that the cost of cloud computing is actually way higher than running your old datacenters (yes, I'm old enough to have run datacenters) I'm actually quite annoyed by the current landscape of "resume-driven" "cloud computing" anyway, and for precisely this reason. In addition to dealing with the complexities of massively scalable distributed systems, we now have to deal with the impossible-to-understand complexities of AWS billing.

Amazon, if you ever stumble across this, you suck. Your services are appalling. They frequently blip out for various unknowable reasons, and your complex billing structure is outrageous and overpriced. I realize Amazon, of course, gives zero f***s about this. They have a very good marketing team and get to laugh all the way to the bank.

To that end, I've recently discovered a NoSQL database that actually appears to deliver on its promises: ScyllaDB. And the cool thing is that if you're already locked in the AWS ecosystem, you can run it there!

This post intends to introduce you to running it locally with Scala 3, so you can experiment with it and get comfortable until maybe (if you're lucky) you can convince your organization to simply GTFO DynamoDB. Later I'll do a write-up on how to set up ScyllaDB on AWS for production use, complete with multi-region failure.

## Run ScyllaDB in Docker locally

To execute local code against the ScyllaDB docker image, you'll need some additional options to expose Scylla to the world:

```shell
docker run --rm -it \
    --name scylla \
    -p 127.0.0.1:9042:9042 \
    -d scylladb/scylla \
    --smp 1 \
    --listen-address 0.0.0.0 \
    --broadcast-rpc-address 127.0.0.1
```

{{< alert >}}
**Note**: It may take several seconds for the container to start, especially on macOS. Give it several seconds, then try running the command below to see if ScyllaDB is up and running.
{{< /alert >}}

Check status:

```shell
docker exec -it scylla nodetool status
```

Once you see output similar to this, ScyllaDB is up and ready to accept connections:

```text
Datacenter: datacenter1
=======================
Status=Up/Down
|/ State=Normal/Leaving/Joining/Moving
--  Address  Load       Tokens       Owns    Host ID                               Rack
UN  0.0.0.0  103.35 KB  256          ?       e94730d9-292d-4501-a890-69c8f2e91619  rack1

Note: Non-system keyspaces don't have the same replication settings, effective ownership information is meaningless
```

You can add additional nodes:

```shell
docker run --name scylla-node2 -d scylladb/scylla --seeds="$(docker inspect --format='{{ .NetworkSettings.IPAddress }}' scylla)"
docker run --name scylla-node3 -d scylladb/scylla --seeds="$(docker inspect --format='{{ .NetworkSettings.IPAddress }}' scylla)"
```

Cqlsh client connect:

```shell
docker exec -it scylla cqlsh
```

## ScyllaDB keyspace

Create keyspace:

```shell
create keyspace mutants
with replication = {'class': 'SimpleStrategy', 'replication_factor' : 3};
```

Switch to the keyspace:

```shell
use mutants;
```

## Create table

```shell
create table mutants (id int primary key, name text);
```

Insert row:

```shell
insert into mutants (id, name) values (1, 'Godzilla');
```

## Scala code

Scala 3 example code available in my [scala3-scylladb](https://github.com/mattmoore/scala3-scylladb) repo.
