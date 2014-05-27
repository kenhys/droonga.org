---
title: "Droonga tutorial: How to add a new replica to an existing cluster?"
layout: en
---

* TOC
{:toc}

## The goal of this tutorial

Learning steps to add a new replica node, and replace a dead replica with new one, for your existing [Droonga][] cluster.

## Precondition

* You must have an existing Droonga cluster with some data.
  Please complete the ["getting started" tutorial](../groonga/) before this.
* You must know how to duplicate data between multiple clusters.
  Please complete the ["How to backup and restore the database?" tutorial](../dump-restore/) before this.

## What's "replica"?

There are two axes, "replica" and "slice", for Droonga nodes.

All "replica" nodes have completely equal data, so they can process your requests (ex. "search") parallelly.
You can increase the capacity of your cluster to process increasing requests, by adding new replicas.

On the other hand, "slice" nodes have different data, for example, one node contains data of the year 2013, another has data of 2014.
You can increase the capacity of your cluster to store increasing data, by adding new slices.

Currently, for a Droonga cluster which is configured as a Groonga compatible system, only replicas can be added, but slices cannot be done.
We'll improve extensibility for slices in the future.

Anyway, this tutorial explains how to add a new replica node to an existing Droogna cluster.
Here we go!

## Add a new replica node to an existing cluster

In this case you don't have to stop the cluster working, for any read-only requests like "search".
You can add a new replica, in the backstage, without downing your service.

On the other hand, you have to stop inpouring of new data to the cluster until the new node starts working.
(In the future we'll provide mechanism to add new nodes completely silently without any stopping of data-flow, but currently can't.)

Assume that there is a Droonga cluster constructed with two replica nodes `192.168.0.10` amd `192.168.0.11`, and we are going to add a new replica node `192.168.0.12`.

### Setup a new node

First, prepare a new computer, install required softwares and configure them.

    (on 192.168.0.12)
    # apt-get update
    # apt-get -y upgrade
    # apt-get install -y ruby ruby-dev build-essential nodejs npm
    # gem install droonga-engine
    # npm install -g droonga-http-server
    # mkdir ~/droonga

Then, remember the command line you executed to generate `catalog.json` for your cluster.
It was:

    (on 192.168.0.10 or 192.168.0.11)
    # droonga-engine-catalog-generate --hosts=192.168.0.10,192.168.0.11 \
                                      --output=~/droonga/catalog.json

For the new node, you have to generate a `custom.json` includes only one node, with same options except the `--host` option, like:

    (on 192.168.0.12)
    # droonga-engine-catalog-generate --hosts=192.168.0.12 \
                                      --output=~/droonga/catalog.json

Let's start the server.

    (on 192.168.0.12)
    # host=192.168.0.12
    # droonga-engine --host=$host \
                     --daemon \
                     --pid-file=~/droonga/droonga-engine.pid
    # droonga-http-server --port=10041 \
                          --receive-host-name=$host \
                          --droonga-engine-host-name=$host \
                          --daemon \
                          --pid-file=~/droonga/droonga-http-server.pid

Then there are two Droonga clusters on this time.

 * The existing cluster including two replicas.
   Let's give a name *"alpha"* to it, for now.
   * `192.168.0.10`
   * `192.168.0.11`
 * The new cluster including just one replica.
   Let's give a name *"beta"* to it, for now.
   * `192.168.0.12`

### Stop inpouring of "write" requests

Before starting  duplication of data, you must stop inpouring of "write" requests to the cluster alpha, because we have to synchronize data in clusters alpha and beta completely.
Otherwise, the new added replica node will contain incomplete data.
Because data in replicas will be inconsistent, results for any request to the cluster become unstable.

What's "write" request?
In particular, these commands modify data in the cluster:

 * `add`
 * `column_create`
 * `column_remove`
 * `delete`
 * `load`
 * `table_create`
 * `table_remove`

If you load new data via the `load` command triggered by a batch script started as a cronjob, disable the job.
If a crawler agent adds new data via the `add` command, stop it.
If you put a fluentd as a buffer between crawler or loader and the cluster, stop outgoing messages from the buffer. 

### Duplicate data from the existing cluster to the new replica

Duplicate data from the cluster alpha to the cluster beta.
It can be done by `drndump` and `droonga-request` commands.
(You have to install `drndump` and `droonga-client` gem packages.)

    (on 192.168.0.12)
    # drndump --host=192.168.0.10 \
              --receiver-host=192.168.0.12 | \
        droonga-request --host=192.168.0.12 \
                        --receiver-host=192.168.0.12

Note that you must specify the host name or the IP address of the machine via the `--receiver-host` option.
If you run the command line on the node `192.168.0.11`, then:

    (on 192.168.0.11)
    # drndump --host=192.168.0.10 \
              --receiver-host=192.168.0.11 | \
        droonga-request --host=192.168.0.12 \
                        --receiver-host=192.168.0.11

### Join the new replica to the cluster

After the duplication is successfully done, join the new replica to the existing clster.
Re-generate the `catalog.json` on the newly joining node `192.168.0.12`, with all nodes specified via the `--hosts` option, like:

    (on 192.168.0.12)
    # droonga-engine-catalog-generate --hosts=192.168.0.10,192.168.0.11,192.168.0.12 \
                                      --output=~/droonga/catalog.json

And restart servers on the new node:

    (on 192.168.0.12)
    # kill $(cat ~/droonga/droonga-engine.pid)
    # kill $(cat ~/droonga/droonga-http-server.pid)
    # host=192.168.0.12
    # droonga-engine --host=$host \
                     --daemon \
                     --pid-file=~/droonga/droonga-engine.pid
    # droonga-http-server --port=10041 \
                          --receive-host-name=$host \
                          --droonga-engine-host-name=$host \
                          --daemon \
                          --pid-file=~/droonga/droonga-http-server.pid

Then there are two Droonga clusters on this time.

 * The existing cluster "alpha", including two replicas.
   * `192.168.0.10`
   * `192.168.0.11`
 * The new cluster including three replicas.
   Let's give a name *"charlie"* to it, for now.
   * `192.168.0.10`
   * `192.168.0.11`
   * `192.168.0.12`

Note that the temporary cluster named "beta" is gone.
And, the new node `192.168.0.12` knows the cluster charlie includes three nodes, other two existing nodes don't know that.
Because both two existing nodes think that there are only them in the cluster they belong to, any incoming request to them never delivered to the new replica `192.168.0.12` yet.

Next, copy new `catalog.json` from `192.168.0.12` to others and restart servers:

    (on 192.168.0.10)
    # kill $(cat ~/droonga/droonga-engine.pid)
    # kill $(cat ~/droonga/droonga-http-server.pid)
    # scp 192.168.0.12:~/droonga/catalog.json ~/droonga/
    # host=192.168.0.10
    # droonga-engine --host=$host \
                     --daemon \
                     --pid-file=~/droonga/droonga-engine.pid
    # droonga-http-server --port=10041 \
                          --receive-host-name=$host \
                          --droonga-engine-host-name=$host \
                          --daemon \
                          --pid-file=~/droonga/droonga-http-server.pid

    (on 192.168.0.11)
    # kill $(cat ~/droonga/droonga-engine.pid)
    # kill $(cat ~/droonga/droonga-http-server.pid)
    # scp 192.168.0.12:~/droonga/catalog.json ~/droonga/
    # host=192.168.0.11
    # droonga-engine --host=$host \
                     --daemon \
                     --pid-file=~/droonga/droonga-engine.pid
    # droonga-http-server --port=10041 \
                          --receive-host-name=$host \
                          --droonga-engine-host-name=$host \
                          --daemon \
                          --pid-file=~/droonga/droonga-http-server.pid

Then there are just one Droonga clusters on this time.

 * The new cluster "charlie",including three replicas.
   * `192.168.0.10`
   * `192.168.0.11`
   * `192.168.0.12`

Note that the old cluster named "alpha" is gone.
Now the new cluster "charlie" with three replicas works perfectly, instead of the old one with two replicas.

### Restart inpouring of "write" requests

TBD

## Replace a broken replica node in a cluster with a new node

### Unjoin the broken replica from the cluster

TBD

### Add a new replica

TBD

## Conclusion

In this tutorial, you did add a new replica node to an existing [Droonga][] cluster.
Moreover, you did replace a dead replica with a new one.

  [Ubuntu]: http://www.ubuntu.com/
  [Droonga]: https://droonga.org/
  [Groonga]: http://groonga.org/
  [command reference]: ../../reference/commands/