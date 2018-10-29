# Architecture

## Spark

Apache Spark uses a **master-slave** architecture. Each Spark application runs independently, the **driver** program is responsible for coordinating/scheduling executors and tasks. When the a Spark application is submitted, the **SparkContext** object in **driver** program will connect to **cluster manager** and ask for **executors**, after executors are assigned, **SparkContext** will send application code and tasks instruction to **executors** for further computation.

![Spark Architecture](../.gitbook/assets/screen-shot-2018-03-06-at-4.41.19-am.png)

## Spark on Kubernetes

When a Spark application is submitted to the **Master Node** of Kubernetes cluster, a **driver pod** will be created first. Once the **driver** **pod** is up and running, it will communicate back to **Master Node** and asks for **executor pods** creation, once the **executor pods** are created, they will communicate with driver pod and start accepting **tasks**.

![Spark On Kubernetes Architecture](../.gitbook/assets/screen-shot-2018-03-17-at-10.56.22-am.png)



