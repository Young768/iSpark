# iSpark -- Elastic Executor Provisioning for Iterative Workloads on Apache Spark


#1. Introduction
------- 
We present an elastic utilization aware executor provisioning approach for iterative workloads on Apache Spark (i.e., iSpark).

#2. Prerequisites
------- 
Spark 2.3.1, Hadoop 2.8.1, Ubuntu 16.04.2 LTS (Kernel 4.4.0), OpenJDK, Scala 2.11.6

#3. Buidling iSpark
------- 
`./build/mvn -Pyarn -Phadoop-2.8 -Dhadoop.version=2.8.1 -DskipTests clean package` 

#4. Configuration
------- 
`spark.dynamicAllocation.enabled` --`true` ;

` spark.eventLog.enabled` --`true` ;

on each slave ` mkdir tmp`;

#5. Workloads
------- 
We use the the workloads from HiBench![](https://github.com/intel-hadoop/HiBench).

#6. Overview of iSpark
------- 
`iMetricCollector` collects the real-time information (e.g. CPU and memory metrcis) and the operation logic information, i.e. RDD (Resilient Distributed Datasets) dependency, from DAG scheduler. `Monitor` will send the load information of corresponding executors to `iMetricCollector` periodically via the system heartbeat.

`iController` makes the allocation decisions based on the metrics provided by `iMetricsCollector`, which will request ExecutorAllocationManager (EAM) to perform the scheduling decision for specific number of executors. Correspondingly, if one executor is deallocated, `iController` will communicate with `TaskScheduler` and mark this executor to be unavailable for other tasks in the remaining execution.
	
`iCacheManager` coordinates with `iController` to ensure the data consistency while preempting executors, which prevents the overhead from losing the intermediate results. `iCacheManager` is responsible for managing RDDs, which applies DAG-aware policy to preserve data partitions. It will update the RDD information to DAG scheduler.
	
`iBlockManager` replicates the data blocks on the executors to be removed based on the scheduling decision and the DAG-aware policy provided by `iCacheManager`. When all the data blocks have been replicated, the corresponding executors are marked as unavailable.



