# Spark On Kubernetes

Starting from 2.3.0, Spark supports Kubernetes as one of its scheduler backend options\([SPARK-18278](https://issues.apache.org/jira/browse/SPARK-18278)\). 

According Databricks Webinar - [What's New in the Upcoming Apache Spark 2.3 Release?](http://go.databricks.com/databricks-runtime-4-with-apache-spark2-3) As the first release having this feature, Spark 2.3.0 Supports:

* Kubernetes 1.6 and up
* Cluster mode only
* Static resource allocation only
* Java and Scala applications
* local or remotely downloadable dependencies

Although the Spark and Kubernetes are not fully integrated yet, it does enable some use cases, in this chapter, we are going through an example usage by using Spark on [Google Kubernetes Engine](https://cloud.google.com/kubernetes-engine/).

