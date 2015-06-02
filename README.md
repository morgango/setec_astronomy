![setec_astronomy](https://github.com/morgango/seatec_astronomy/blob/master/setec.gif)

There are a lot of interesting features in [Elasticsearch](http://elastic.co) and the [ELK stack](https://www.elastic.co/webinars/introduction-elk-stack).  Really, really, interesting features that you need to get your hands on.

However, for you to get a real understanding of what it can do, you need to get your hands on a fully featured, functioning version of the software.  Not a demo, not a simplification, but an actual cluster that you can touch and feel and easily expand if that is something you are interested in. 

Well, this project does just that.  It uses `docker` and `docker-configure` to build a fully functioning 5-node Elasticsearch cluster along with Kibana.  [Logstash](http://logstash.net) isn’t included, because in large part another container with just the Logstash JAR file wouldn’t be a big help to learning the tool (it would be much easier to run logstash on your local machine and point it at the docker container).


## What you need to do

1. Download and Install [Docker](https://docs.docker.com/installation/)
1. Download and Install [Docker Compose](https://docs.docker.com/compose/install/)
3. Download and Install the command line completions for both tools (optional).
4. Clone [this repo](https://github.com/morgango/seatec_astronomy.git)
5. Move into the directory you just created.
6. Run `docker-compose up`


## What should happen

If all goes well, you should see the log output from the various containers all running

There will be several containers running:

* **client** - an Elasticearch client node with port `9200` exposed to the world.  This is how `curl` or Kibana (or anythnig else) will connect to the cluster.
* **master** - an Elasticearch master client node, mostly for show.
* **es<n>** - an Elasticearch data node.  By default there are 3 data nodes, this could easily be expanded if necessary 
* **kb** - a Kibana instance with port `5601` exposed to the world.  You can point your web browser at this to hit the cluster.

### Configuration

All the nodes can be configured from outside the containers.  In the repo, you will see a data directory, which is mounted as a read/write volume for each of the containers.

* **client** -  uses `data/client.yml`
* **master** -  uses `data/master.ymli`
* **es<n>** - uses `data/elasticsearch.yml`
* **kb** - uses `data/kibana.yml`

The nodes should make a `data/data/setec_astronomy/` that will hold all the physical data created by the nodes, which is very useful for examining what is happening under the hood.  If you restart the containers, it will keep the underlying data

## Hints

###Logging in to a running container

``` bash
# run a one-off command and send the output to the screen
docker-compose run <container> <command>
```

``` bash
# open a shell in one of the running containers, so that multiple commands can be run
docker-compose run <container> bash
# It may not display the shell right away, if not just press Enter
```

###Restart all containers

``` bash
docker-compose restart
```
###Rebuild all containers

``` bash
docker-compose build
```

## Troubleshooting

`kb_1     | {"@timestamp":"2015-06-02T19:28:22.300Z","level":"info","message":"Unable to connect to elasticsearch at http://client:9200. Retrying in 2.5 seconds.","node_env":"production"}`

It is not uncommon that the underlying data becomes corrupted if you are bringing the systems up and down a lot.  Delete the `data/data/setec_astronomy/` directory and reload your data.





