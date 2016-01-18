# cloud-local

Cloud-local is a collection of bash scripts to set up a single-node cloud on on your desktop, laptop, or NUC. Performance expectations are to be sufficient for testing things like map-reduce ingest, converters in real life with real files, and your own geoserver/iterator stack. This setup is preconfigured to run YARN so you can submit command line tools mapreduce jobs to it. Currently localhost ssh is NOT required so it will work on a NUC. 

## Getting Started 

To prepare for the first run of cloud-local, you may need to `unset` environment variables `HADOOP_HOME`, `ACCUMULO_HOME` , `ZOOKEEPER_HOME`, and others. If `env |grep -i hadoop` comes back empty, you should be good to go. You should also `kill` any instances of zookeeper or hadoop running locally. Find them with `jps -lV`.

When using this the first time...

    git clone git@github.com:ccri/cloud-local.git
    cd cloud-local
    bin/cloud-local.sh init

You'll be prompted to enter the accumulo instance name and pasword...(hint: try "local" and "secret")

This init script does several things:
* configure HDFS configuration files
* format the HDFS namenode
* create a user homedir in hdfs
* initialize accumulo
* start up zookeeper, hadoop, and accumulo

After running `init` source the variables in your bashrc or other shell:

    source bin/config.sh

Now you should have the environment vars set:
    
    env | grep -i hadoop

Now you can run fun commands like:

    hadoop fs -ls /
    accumulo shell -u root 

After installing it you should be able to reach your standard cloud urls:

* Accumulo:    http://localhost:50095
* Hadoop DFS:  http://localhost:50070
* Job Tracker: http://localhost:8088

There are a few other scripts:

* ```bin/start_cloud.sh``` - Start the cloud (make sure it isn't running)
* ```bin/stop_cloud.sh``` - Stop the cloud safely...hopefully


## Maintenance

If you foobar your cloud, you can just delete everything and start over. You should do this once a week or so just for good measure.  

    cd $CLOUD_HOME
    bin/stop_cloud.sh  #if cloud is running
    rm * -rf
    git pull
    git reset --hard
    bin/cloud-local.sh init

The `cloud-local.sh` script provides some less drastic options. The parameter `clean` will remove software (but not the tar.gz's) and data. The parameter `reconfigure` will first `clean` then `init`.
