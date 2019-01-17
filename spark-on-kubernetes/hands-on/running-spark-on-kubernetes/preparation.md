# Preparation

## Create Google Kubernetes Engine

Please refer to the [Quickstart](https://cloud.google.com/kubernetes-engine/docs/quickstart) Guide about how to create a GKE cluster, get authentication credentials for the cluster and use **kubectl** command line tool.

## Prepare Spark 2.4 docker images

Download Spark 2.4 from [Apache Spark website](https://spark.apache.org/downloads.html)

```text
$ curl -O http://mirrors.koehn.com/apache/spark/spark-2.4.0/spark-2.4.0-bin-hadoop2.7.tgz
$ tar -zxvf spark-2.4.0-bin-hadoop2.7.tgz
$ ls spark-2.4.0-bin-hadoop2.7
LICENSE    README.md  conf       jars       python
NOTICE     RELEASE    data       kubernetes sbin
R          bin        examples   licenses   yarn
```

Download [Google Cloud Storage Connector for Hadoop 2.x](https://cloud.google.com/dataproc/docs/concepts/connectors/cloud-storage), [Hadoop AWS 2.7.x](https://mvnrepository.com/artifact/org.apache.hadoop/hadoop-aws/2.7.3) and [AWS SDK for Java](https://aws.amazon.com/sdk-for-java/), copy the jar files to _**jars/**_ in Spark, these are the dependency jar files in order to leverage [Google Cloud Storage](https://cloud.google.com/storage/) and [Amazon S3](https://aws.amazon.com/s3/) as the data/log store.

```text
$ curl -O https://storage.googleapis.com/hadoop-lib/gcs/gcs-connector-latest-hadoop2.jar
$ curl -O http://central.maven.org/maven2/org/apache/hadoop/hadoop-aws/2.7.3/hadoop-aws-2.7.3.jar
$ curl -O http://central.maven.org/maven2/com/amazonaws/aws-java-sdk/1.7.4/aws-java-sdk-1.7.4.jar
$ cp gcs-connector-latest-hadoop2.jar hadoop-aws-2.7.3.jar aws-java-sdk-1.7.4.jar spark-2.4.0-bin-hadoop2.7/jars/
```

Build Spark 2.4 Docker Image, Spark 2.4 provides a Dockerfile which we can use directly to build docker images. Please refer to [Docker Doc](https://docs.docker.com/) about how to install docker on your local laptop and Create Docker ID. Once the image is built, we need to push it to Docker Registry such as [Docker Hub](https://hub.docker.com/), [Google Container Registry](https://cloud.google.com/container-registry/), [Harbor](https://github.com/vmware/harbor), etc.

```text
$ cd spark-2.4.0-bin-hadoop2.7
$ docker build -f ./kubernetes/dockerfiles/spark/Dockerfile . -t azureq/pantheon:spark-2.4
$ docker build -f ./kubernetes/dockerfiles/spark/bindings/python/Dockerfile . -t azureq/pantheon:pyspark-2.4 --build-arg base_img=azureq/pantheon:spark-2.4
$ docker build -f ./kubernetes/dockerfiles/spark/bindings/R/Dockerfile . -t azureq/pantheon:rspark-2.4 --build-arg base_img=azureq/pantheon:spark-2.4
$ docker push azureq/pantheon:spark-2.4
$ docker push azureq/pantheon:pyspark-2.4
$ docker push azureq/pantheon:rspark-2.4
```

Now you have a Spark 2.4 docker image with GCS and Amazon S3 connector built in.

