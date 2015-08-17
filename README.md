![setec_astronomy](https://github.com/morgango/seatec_astronomy/blob/master/setec.gif)

There are a lot of interesting features in [Elasticsearch](http://elastic.co) and the [ELK stack](https://www.elastic.co/webinars/introduction-elk-stack).  Really, really, interesting features that you need to get your hands on.

However, for you to get a real understanding of what it can do, you need to get your hands on a fully featured, functioning version of the software.  Not a demo, not a simplification, but an actual cluster that you can touch and feel and easily expand if that is something you are interested in. 

Well, this project does just that.  It uses `docker` and `docker-configure` to build a fully functioning 5-node Elasticsearch cluster along with Kibana.  [Logstash](http://logstash.net) isn’t included, because in large part another container with just the Logstash JAR file wouldn’t be a big help to learning the tool (it would be much easier to run logstash on your local machine and point it at the docker container).

## What you need to do

![screenshot](https://github.com/morgango/seatec_astronomy/blob/master/screen.gif)

1. Download and Install [Docker](https://docs.docker.com/installation/)
1. Download and Install [Docker Compose](https://docs.docker.com/compose/install/)
3. Download and Install the command line completions for both tools (optional).
4. Clone [this repo](https://github.com/morgango/seatec_astronomy.git)
5. Move into the directory you just created.
6. Run `docker-compose up`

## What should happen

If all goes well, you should see the log output from the various containers all running the base ELK stack

There will be several containers running:

* **client** - an Elasticearch client node with port `9200` exposed to the world.  This is how `curl` or Kibana (or anything else) will connect to the cluster.
* **master** - an Elasticearch master client node, mostly for show.
* **es<n>** - an Elasticearch data node.  By default there are 3 data nodes, this could easily be expanded if necessary 
* **kb** - a Kibana instance with port `5601` exposed to the world.  You can point your web browser at this to hit the cluster.

## Different Options

There are a number of .yml files that can be used to build Elasticsearch clusters.

* `es-single-install-only.yml` will build a single container that has Elasticsearch installed, but will not be running.  You will need to log into the machine and run the command line yourself. 
* `es-single-no-plugins.yml` will build a single container that has Elasticsearch installed and running with no plugins.
* `es-single-with-plugins.yml` will build a single container that has Elasticsearch installed and running with all the Elastic commercial plugins (Shield, Watcher, Marvel).
* `es-cluster-install-only.yml` will build a cluster container that has the Elasticsearch and Kibana installed, but will not be running.  You will need to log into the machine, and run the command lines yourself. 
* `es-cluster-no-plugins.yml` will build a cluster that has Elasticsearch and Kibana installed and running with no plugins. This should be be fully functional right out-of-the-box.
* `es-cluster-with-plugins.yml` will build a cluster container that has Elasticsearch and Kibana installed and running with all the Elastic commercial plugins (Shield, Watcher, Marvel). This cluster is not in a useable state, because the commercial plugins are not configured.  You will need to log on to each machine and confgure them properly.

These are fed to the `docker-compose` tool with a command line like:

```docker-compose --file=<filename you are using> up```

### Configuration

It is probably easiest to do your configuration in the command line in the docker compose **YML** files themselves.  For example:

```bash
command: /usr/share/elasticsearch/bin/elasticsearch 
       -Des.cluster.name="setec_astronomy" 
       -Des.node.master="true" 
       -Des.node.data="true" 
       -Des.node.client="false"
``` 

In this case, Elasticsearch can be configured without requiring a configuration file to be added to the Dockerfile or image itself, which allows us to use the official version.

If you need to provide a configuration file, you can use a command like:

```bash
command: /usr/share/elasticsearch/bin/elasticsearch 
       -Des.config=/data/elasticsearch.yml
```

This will allow you to provide configuration without having to create your own Dockerfile. Also, note that the image has a [volume](https://docs.docker.com/userguide/dockervolumes/) mounted as the /data directory which is already configured.  While unfortunately named, it does allow some visibility outside the container, which can be very useful.

**NOTE**: These configuration options are specified in the  `docker-compose.yml` file.  You can point them to any location in the  `data` directory at run time.

By default, he nodes should make a `data/data/setec_astronomy/` that will hold all the physical data created by the nodes, which is very useful for examining what is happening under the hood.  If you restart the containers, it will keep the underlying data

## Hints

###Running multiple commands in docker
``` bash
command: sh -c 'plugin -i elasticsearch/license/latest\
            && plugin -i elasticsearch/shield/latest\
            && plugin -i elasticsearch/marvel/latest\
            && plugin -i elasticsearch/watcher/latest\
            && /bin/sleep infinity'
# this will run multiple commands, the ‘\’ and ‘&&“ are required for multiline.
```

###Configure Elasticsearch if restart is required.
``` bash
# The sleep command ensures that something is running to keep the container is alive, but it isn’t Elasticsearch.
command: /bin/sleep infinity
# You can now use docker exec to login, configure, and start the Elasticsearch command on your own (remember to use -d)
```

###Logging in to a running container
``` bash
# automatically build and run a file with docker compose
docker-compose --file=<filename you are using> up
# NOTE: You should be in the TOP LEVEL directory to run this command.
```

``` bash
# run a one-off command and send the output to the screen
docker exec -it <container>_1 bash
# NOTE: This is a `docker` command, not a `docker-compose` command.
```

``` bash
# open a shell in one of the running containers, so that multiple commands can be run
docker exec -it <container>_1 bash
# NOTE: This is a `docker` command, not a `docker-compose` command.
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

```bash
kb_1     | {"@timestamp":"2015-06-02T19:28:22.300Z","level":"info","message":"Unable to connect to elasticsearch at http://client:9200. Retrying in 2.5 seconds.","node_env":"production"}
```

It is not uncommon that the underlying data becomes corrupted if you are bringing the systems up and down a lot.  Delete the `data/data/setec_astronomy/` directory and reload your data.
