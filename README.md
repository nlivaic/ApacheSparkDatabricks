## Architecture

### High level arhitecture

![High level arhitecture](https://github.com/nlivaic/ApacheSparkDatabricks/assets/26722936/25b84447-23f7-4eb2-ba1a-bb0a4b2e8f25)

### Runtime arhitecture

![Runtime arhitecture](https://github.com/nlivaic/ApacheSparkDatabricks/assets/26722936/1e165367-e350-435d-a7b0-032c595893cb)

* Driver Program:
    * What the user interacts with. Executes code and interacts with `SparkContext`.
    * Separate process with its own JVM running on master node.
    * Launches tasks.
    * Hosts `SparkContext`, which is a way to programmatically access to Spark environment.
    * Has several groups of services running inside of it:
        * `SparkEnv` holds the environment our program is running in.
        * `DAGScheduler` runs the DAG of transformations.
        * `TaskScheduler` runs tasks performing data processing operations.
        * `SparkUI` allows monitoring application.
* Spark Application:
    * Code we write to process data. Uses `SparkContext` as an entry point to access Spark environment.
    * Creates a Directed Acyclic Graph representing steps taken as part of a transformation.
    * Spark creates Stages (physical execution plan).
    * Each stage is split into Tasks. These are operations on RDD partitions.
* `SparkSession` encapsulates different execution contexts (`SparkContext`, `SQLContext`, `HiveContext`).
* Cluster Manager:
    * Driver Program interacts with the Cluster Manager.
    * Responsible of all resource allocation in Spark. Spark supports multiple resource managers (Hadoop's YARN, Apache Mesos, k8s, Spark Standalone), but it does not care which one is used.
* Worker is a compute node in charge of running Spark application code. It executes tasks.
* Tasks are basic units of execution that do actual processing on our data.
* Tasks belong inside stages.
* Stages are physical units of execution.
* So, to sum up: when you programatically access `SparkContext` and run queries in your code, `SparkContext` running within your Driver Program will in turn talk to Cluster Manager and it will in turn talk to each Worker and each Worker will in turn spin up executors to run processing.

# Data structures and execution

## RDDs (Resilient Distributed Datasets)

* All operations in Spark are performed on in-memory objects for performance reasons. These are called RDDs.
* RDD is a collection of entities (rows or records).
* RDD is a basic data structure.
* Characteristics:
    * Partitioned - split across nodes in a cluster
    * Immutable - once created cannot be changed. To make a change a new RDD has to be created through transformations.
    * Resilient - can be reconstructed on a node crash.
* Even though RDD is split across nodes, we can access it using a single variable.
* Aren't used directly in Spark 3 anymore, but are still the main building block.

## Transformations, Actions, Lazy Evaluation

* Transformations transform input RDDs into another RDDs.
* Action requests a result to a file or a console window.
* RDDS report lazy evaluation. As we request transformations, Spark keeps a record of transformations - these transformations are applied in one go only when user requests a result.
* This record of transformations is called __lineage__. This allows Spark to reconstruct RDDs in case of a crash.

## Spark APIs

![Spark APIs](https://github.com/nlivaic/ApacheSparkDatabricks/assets/26722936/1b9f1edb-97b9-47be-ad29-f77c5786cd43)
* Two levels of APIs are marked on above image:
    * Low-level APIs working with RDDs. We won't be working with these.
    * High-level APIs working with Datasets and DataFrames.
* Datasets are type safe, don't have named columns and are accessible only within type safe languages. As we will be working only with Python these are of no interest to us.

## DataFrames

* Built on top of RDDs. All the characteristics of RDDs apply to DataFrames as well.
* This is what we will be working with most of the time.
* We can query a dataframe using SQL.
