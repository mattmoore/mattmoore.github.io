---
title: "Scala 3 with ScyllaDB"
summary: "Using ScyllaDB locally using the ScyllaDB Java Driver with Scala 3."
date: 2024-01-17T17:36:48-05:00
draft: false
---

## What is ScyllaDB?

[ScyllaDB](https://www.scylladb.com) is a NoSQL database that a lot of high-scale companies have been switching to lately (including Discord, notably). While ScyllaDB is written in Rust, it is possible to use it with Scala 3.

The ScyllaDB docs mention using phantom (originally intended for Cassandra). However, phantom is not currently compatible with Scala 3 as it makes use of Scala 2 macros, which would have to be re-written for Scala 3's new macro system. ScyllaDB does work quite well with Scala 3 using the Scylla Java drivers.

This post is about local development with ScyllaDB and Scala 3. I plan to write a post soon about deploying ScyllaDB to AWS for production.

I took an interest in ScyllaDB because I had to work with DynamoDB last year and the experience was quite painful. Perhaps I'll write another article on all the pain points later (and why I think DynamoDB is not production-ready software), but in this post I'm focused mainly on getting ScyllaDB up and running locally with docker to use with Scala 3.

## Run ScyllaDB in Docker

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
insert into mutants (id, name) values (2, 'Scylla');
```

## Scala code

Scala 3 example code available in my [scala3-scylladb](https://github.com/mattmoore/scala3-scylladb) repo.
