# Flight Listener

This application will listen for flight data from the air-traffic-control
service on an Apache Kafka topic. For any flight that it detects a late
departure it will rebroadcast the flight info on a second topic.

## usage on openshift

As this project utilizes Spark, it will be easiest to consume on OpenShift by
using the [radanalytics.io](https://radanalytics.io) tooling. The source-to-image
nature of this application will require that a Spark cluster is available. The
shortest path to making that connection is to use the automatically spawned
Spark clusters that are created by the
[Oshinko project source-to-image](https://github.com/radanalyticsio/oshinko-s2i)
utilities. Please see that documentation for more information about this
process.

This application also requires the deployment of a Kafka broker and something
to emit messages to that broker. These topics are out of scope for this
tutorial, but please see the (Strimzi documentation)[https://strimzi.io]
for some inspiration on prototyping this process.

1. see the [radanalytics.io Get Started page](https://radanalytics.io/get-started)
   for instructions on installing that tooling

1. launch the skeleton with the following command:
   ```bash
   oc new-app --template=oshinko-python-spark-build-dc \
              -p APPLICATION_NAME=flight-listener \
              -p GIT_URI=https://github.com/elmiko/late-departures \
              -p CONTEXT_DIR=flight-listener \
              -e KAFKA_BROKERS=my-cluster-kafka:9092 \
              -e KAFKA_INTOPIC=flights \
              -e KAFKA_OUTTOPIC=late \
              -p SPARK_OPTIONS='--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.1.0'
   ```

In this example, our application will subscribe to messages on the Kafka topic
`flights`, and it will publish messages on the topic `late` using the broker
at `my-cluster-kafka:9092`.
