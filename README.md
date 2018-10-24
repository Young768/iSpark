# iSpark -- Flexible Executor Management for Iterative Workloads on Apache Spark
#1. Introduction
------- 
we present a flexible utilization aware executor management approach for iterative workloads on Apache Spark (i.e. iSpark).

#2. Prerequisites
------- 
Spark 2.3.1, Hadoop 2.8.1, Ubuntu 16.04.2 LTS (Kernel 4.4.0), OpenJDK, Scala 2.10.4

#3. Buidling iSpark
------- 
`./build/mvn -Pyarn -Phadoop-2.8 -Dhadoop.version=2.8.1 -DskipTests clean package` 

#4. Configuration
------- 
`spark.dynamicAllocation.enabled` --`true` ;
` spark.eventLog.enabled` --`true` ;
on each slave, ` mkdir /tmp`;

#5.Workloads
------- 
We use the the workloads from HiBench![](https://github.com/intel-hadoop/HiBench).

#6. overview of iSpark
------- 
	\item $\emph{iMetricCollector}$ collects the real-time information (e.g. CPU and memory metrcis) and the operation logic information, i.e. RDD (Resilient Distributed Datasets) dependency, from DAG scheduler. $\emph{Monitor}$ will send the load information of corresponding executors to $\emph{iMetricCollector}$ periodically via the system heartbeat.
	
	\item $\emph{iController}$ makes the allocation decisions based on the metrics provided by $\emph{iMetricsCollector}$, which will request $\emph{ExecutorAllocationManager (EAM)}$ to perform the scheduling decision for specific number of executors. Correspondingly, if one executor is deallocated, $\emph{iController}$ will communicate with $\emph{TaskScheduler}$ and mark this executor to be unavailable for other tasks in the remaining execution.
	
	\item $\emph{iCacheManager}$ coordinates with $\emph{iController}$ to ensure the data consistency while preempting executors, which prevents the overhead from losing the intermediate results. $\emph{iCacheManager}$ is responsible for managing RDDs, which applies DAG-aware policy to preserve data partitions. It will update the RDD information to DAG scheduler.
	
	\item $\emph{iBlockManager}$ replicates the data blocks on the executors to be removed based on the scheduling decision and the DAG-aware policy provided by $\emph{iCacheManager}$. When all the data blocks have been replicated, the corresponding executors are marked as unavailable.


P.S. The coding work of iController component is still being reorganized.


