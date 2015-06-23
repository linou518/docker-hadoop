# Docker-hadoop [![Docker registry](https://img.shields.io/badge/docker-registry-blue.svg)](https://registry.hub.docker.com/u/docxs/hadoop/) [![MIT](https://img.shields.io/badge/license-MIT-blue.svg)]()

Hadoop in docker.


## Get the docker image
You can either build it yourself, or pull it from the Docker Registry.

### Build the image
Change directory into the `<hadoop_version>` folder you want to build, then:

```shell
docker build --rm -t docxs/hadoop:<hadoop_version> .
```

### Get it from Docker Registry
```shell
docker pull docxs/hadoop:<hadoop_version>
```


## Running the container

### Environment variables
- `HADOOP_DATA_DIR`: the location of your data within the container. (default: `/data/hadoop/data`)
- `HADOOP_LOG_DIR`: the location of your hadoop/yarn log files within the container (default: `/data/hadoop/log`)


### Options

- `-noformat`: (needs to be the first option) if you don't want an hdfs format to happen upon starting the container (in case you want to keep persistent data)
- `-d`: if you want to run the container in detached mode
- `-bash`: if you want to run the container with a shell session
- `-run`: (followed by a command) if you want the container to immediately execute a command


### Examples

#### Ephemeral data
```shell
docker run -it [--name <name>] \
           docxs/hadoop:<hadoop_version> -bash
```

#### Persistent data
Running it for the first time
```shell
docker run -it [--name <name>] \
           -v <data_folder>:/data \
           docxs/hadoop:<hadoop_version> \
           -bash
```

Running it again...
```shell
docker run -it [--name <name>] \
           -v <data_folder>:/data \
           docxs/hadoop:<hadoop_version> \
           -noformat -bash
```


## Testing it
When attached to the container, you can run the following example
```shell
# create an input folder on hdfs and add some files to it
hdfs dfs -mkdir -p input
hdfs dfs -put $HADOOP_PREFIX/etc/hadoop/*.xml input

# run the example map-reduce job (for 2.7 here)
hadoop jar $HADOOP_PREFIX/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.0.jar grep input output 'dfs[a-z.]+'

# checkout the output
hdfs dfs -cat output/*
```

## Hadoop native libraries
The dockerfile downloads the compiled native libraries from bintray. If you want, you can build them yourself.
All bateries are included in the dockerfile included in the `native_libraries` directory.


## License

Licensed under the MIT License. See the LICENSE file for details.


## Feedback, bug-reports, requests, ...

Are [welcome](https://github.com/docxs/docker-hadoop/issues)!