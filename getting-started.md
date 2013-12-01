Getting started
===============

To get started with Spark on JANUS, download the
[sources](http://spark.incubator.apache.org/downloads.html) and compile them
on an interactive node. You will need to run:

    module load jdk/jdk-1.7.0_17

in order to set up the Java compiler and runtime. Follow the instructions
[here](http://spark.incubator.apache.org/docs/latest/index.html) to compile the
sources and run the examples.

Running on the Cluster
======================

We will follow the
[Spark Standalone Mode documentation](http://spark.incubator.apache.org/docs/latest/spark-standalone.html).
On your interactive node (the master), run:

    bin/start-master.sh

from within the Spark source directory. This should start a server in the background.
Running this command should give you a path, like

    spark-0.8.0-incubating/logs/*.out

This file contains the log for the master node, and will have a line containing
the Spark port (like 7077) and the web UI port (like 8080).

Accessing the Web UI using SSH
------------------------------

Use the \"hostname\" command to figure out the hostname of your node. It will
be something like *node0848*. This node is buried on the JANUS network somewhere,
and we want to reach it at its web UI port (8080). The easiest approach is to
use SSH port forwarding.

Instead of logging into JANUS using:

    ssh user@login.rc.colorado.edu

Use the -L flag like:

    ssh -L8080:node0848:8080 user@login.rc.colorado.edu

This will set up the port 8080 on *your local machine* to point to port 8080 on
*node0848*. Then you can just open 127.0.0.1:8080 in your browser and get
a friendly web UI.

Firing up some worker nodes
---------------------------

Worker nodes connect to your spark server with a command like:

    ./spark-class org.apache.spark.deploy.worker Worker spark://node0848:7077

So write a PBS script like:

    module load jdk/jdk-1.7.0_17
    projects/spark-0.8.0-incubating/spark-class org.apache.spark.deploy.worker.Worker spark://node0848:7077

Write that to a file named *script* run that using qsub:

    qsub -q janus-debug script

This will spark off a worker node which will connect to your master. Refresh
the web UI and you should see the worker node listed there. To start a bunch
of workers, it should suffice to run the qsub command in a loop.
