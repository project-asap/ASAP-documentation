.. highlight:: rst

**************************************
Extension of the Apache Spark
**************************************

Introduction
############


We extended the Apache Spark programming model and scheduler to supoort nested RDD operations, to facilitate expressing recursive and hierarchical computations. Apache Spark is a fast and general cluster computing system for Big Data that uses RDD abstractions. RDDs are immutable partitioned collections that stored in a storage system such as HDFS or derived by applying operators to other RDDs. We also modified the default Spark scheduling mechanism to support many schedulers and not only a central master scheduler. 

.. code:: bash

	val file1 = sc.textFile("hdfs://file1")
	val file2 = sc.textFile("hdfs://file2")
	file1.map(word1 =>
		file2.filter(word2 =>
			(word1.length > word2.length))
				.collect())
			.collect()


Let see the above example code that creates two RDDs froma two HDFS files and performs a map operation on RDD file1. The mapper function of the map operation performs a filter operation on RDD file2 for every word in RDD file1 to select all the words that have the larger length. The collect() function forces the computation and collect the results into an array. By default, Apache Spark does not support such nested RDD operations. Our extension handles nested RDD operators and requires from the executor nodes to behave as the master node. In this example, the scheduler creates tasks that execute the mapper function and distribute it to the executors, then it creates a task for every partition of the file1 RDD and sends each task to an idle executor to run the mapper function on that partition. In the executor nodes, when the mapper function runs, it tries to invoke a filter operation on the file2 RDD. We extended the executor functionality to capture this event and send a CreateRDD message to the scheduler node. The message contains an identifier of the RDD object referenced, the (reflective) name of the invoked operation, and a serialized version of the user-defined function that is applied. We also extended the Spark scheduler to receive messages from the executors. Upon receiving such a forwarded RDD operation message, the scheduler looks up the RDD with the specified id and, invokes the specified operation. 


Link
####

The code of the NestedSpark can be found in 

.. code:: bash

	https://github.com/project-asap/spark01.git




Install
#######

For demostration reasons a Linux operating system like Ubuntu it is assumed in this step. In Windows or other Linux distributions the equivalents should be done.

The build instructions are the following:

1. Install sbt:

.. code:: bash

        	sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 642AC823
		sudo apt-get update
		sudo apt-get install sbt

2. Check the version of the installed Hadoop-yarn

3. Build the spark:

.. code:: bash 

		cd <Spark_home>
	        ./build/sbt -Dhadoop.version=<Hadoop_version> -Pyarn -DskipTests clean assembly		    

4. Configure spark:

.. code:: bash 

		cd <Spark_home>
		cp conf/spark-env.sh.template conf/spark-env.sh

Set the IP of master node in the file conf/spark-env.sh

.. code:: bash 

                cp conf/spark-defaults.conf.template conf/spark-defaults.conf

For every slaves node insert the list of all executor's IPs

.. code:: bash 

For every slaves node execute: cp conf/slaves.template conf/slaves

5. Start spark:

.. code:: bash 

		./sbin/stop-all.sh



Tests
#####

Clone the code of spark tests: 

.. code:: bash

	git clone https://github.com/project-asap/spark-tests.git


1. Build In the <Spark_tests_home> execute:

.. code:: bash

	mkdir -p /lib
	cp <Spark_home>/assembly/target/scala-2.10/spark-assembly-*.jar lib/
	sbt clean package

2. Test the hierarchical, in the <Spark_tests_home>:

.. code:: bash

 	<Spark_home>/bin/spark-submit --class HierarchicalKMeansPar target/scala-2.10/spark-tests_2.10-1.0.jar spark://<Spark_master-ip>:7077 100 2 2 2 <text_file_path> --dist-sched false

3. Test the distributed scheduler, in the <Spark_tests_home>:

.. code:: bash
 
	<Spark_home>/bin/spark-submit --class Run target/scala-2.10/spark-tests_2.10-1.0.jar --master spark://<Spark_master_ip>:7077 --algo Filter33 --dist-sched true --nsched 4 --partitions 32 --runs 15


			 
							    
		    
	            



