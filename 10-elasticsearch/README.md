# Session 10 - Elasticsearch

Grading: 2pt

**This is the final lab of this semester**

## Setup

For this lab, you will create a cluster with 6 nodes - 3 data nodes and 3 master-eligible nodes. You could do it by hand, but to save you a lot oftime, you can use a prepared "configuration". To run this configuration, you will need to install docker-compose. Follow the official instructions for your operating system https://docs.docker.com/compose/install/

With docker-compose installed, download docker-compose.yml configuration file from this repository and save it somewhere to your local disk. Change to the directory where you downloaded docker-compose.yml and run

```
docker-compose up
```

It will take some time for everything to start, you will see plenty of logging output, but after around a minute, the output should stop and you should be able to run a simple `curl localhost:9200` and get some output.

Note that this lab is very resource intensive. Each elasticsearch instance will **lock** 0.5GB of RAM, and it's quite possible that some nodes will not be able to start. If this happens, try decreasing the number of locked memory by modifying the `ES_JAVA_OPTS` environment inside docker-compose.yml

Remember, that pressing Control + C in the docker-compose window will stop all running docker containers.

**It is ok if you work in groups. If you can't get the cluster running, don't worry about it and a friend who can. Lab notes are still your individual work.**

## Labs

1. Check cluster nodes via

```
curl -s 'localhost:9200/_cat/nodes?v'
```

Did all nodes join the cluster? Can you tell which node is current master? If you try the command several times, is the master still the same node, or is it changing?

2. Shut down the current master. For your convenience, the node names are the same as docker container names. To stop a node, simply stop it via docker, e.g.

```
docker stop esmaster01 # note that your master name will probably vary
```

What happened after you stopped the current master? Check `_cat/nodes` again. Did the cluster elect a new master?

What about cluster health? Check `curl 'localhost:9200/_cluster/health?pretty'`. Is the cluster in a green state? Yellow? Or red? Look for "status" in the output json, `grep` is your friend.

**Optional:** Have a look at the logs in docker-compose output. Can you reconstruct a timeline of events which happened after current master quit?

3. While you are at this stage, with 2 masters-eligible nodes, create an index with 2 shards and 1 replica and try indexing some data. It doesn't really matter what you index, just create some index with correct settings and put in a few documents.

Your writes should be accepted without any problems.

4. Stop another master-eligible node. Just to get a point across - try stopping the master-eligible node that is not currently elected master. Your cluster should now be in a bad state. Try listing all nodes via `_cat/nodes`. Did it work? 

Try indexing some data. Be very patient, it will seem to hang, but the indexing command will fail. How did it fail/what was the error message?

5. In this degraded state, try searching for data. A simple search at the `_search` enpoint is enough, do not bother writing a query. Does searching still work?

6. Bring back one of the dead master-eligible nodes

```
docker start esmaster01 # note that your master name will probably vary
```

Try getting a list of nodes, or indexing data. Is it working? Be patient, it may take the cluster some time to get back online.

Bring back the remaining master-eligible node.

**Optional**: Have a look at the logs in docker-compose window and find out what happened after the master-eligible node came up.

7. Inspect your shard layout via

```
curl localhost:9200/_cat/shards
```

Select a node which has some shards and stop it.

Inspect the shard layout again. Note that if you stoped node `es01`, your port 9200 is now dead too. Try port 9201 (es02) or 9202 (es03). Note that these port mapping are set up specifically for this lab, under normal circumstances each node runs on 9200 on its own host.

What is the shard layout now? What changed?

What is the cluster health now? Also check index-level healt by running

```
curl localhost:9200/_cat/indices
```

Try waiting for a while (about a minute should be enough) and check everything again.

8. Take down another node and repeat tasks from 7. Again, keep in mind that by stopping a host, you may have cut off your access via the port you were using. In that case, try using a different port (9200, 9201, 9202)

With only one data node left, the cluster cannot assign replica indices, you should be in yellow state.

9. Try searching and indexing in this state. Are both operations working?

10. Take down the last data node. Access the cluster via master node, via port 9210, 9211 or 9212. What is cluster health now? Index-level health? Shard allocation?

11. Bring back the data nodes one by one and observe what happens. Did you lose any data when all 3 data nodes went down?
