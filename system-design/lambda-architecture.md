# Lambda Architecture

A way of processing big data that  provides access to batch-processing and stream-processing methods with a hybrid approach.

It is composed of 3 layers:
1. Batch layer
2. Serving layer
3. Speed layer

![](https://databricks.com/wp-content/uploads/2018/12/hadoop-architecture.jpg)

## Batch layer

New data gets fed to the batch layer and speed layer simultaneously. It looks at al the data at once and eventually corrects the data in the speed layer.

This layer runs at a predefined schedule, and aggregates data over a period of time before running.

## Serving layer

The outputs from the batch layer gets forwarded to this layer for indexing so that they can be queried in low-latency on an ad-hoc basis (when necessary or needed).

On some systems, outputs from speed layer also gets forwarded here to provide some more granularity (e.g. speed layer batches every 1 minute, and batch layer batches every hour. If user asks for x data from 1.5 hours, use combination of both to come up with the result).

## Speed layer

Also called Stream Layer. Handles the data that are not already delivered in the batch view due to its latency. Only deals with recent data in order to provide a real-time view.

## Benefits of lambda architecture

- No server management
- Flexible scaling
- Automated high availability
- Business Agility

## Real-world usage/scenario

1. Top K heavy hitters (i.e. most viewed video on Youtube, Top K exceptions, etc.)
- Speed layer
  - Use count-min sketch to keep track of the number of hit
  - Use heap to calculate the top K heavy hitters
- Batch layer
  - Have Kafka stream to deliver individual logs
  - Have data partitioner to provide sticky routing for each signature (same element will always go to the same host)
  - Have the host to aggregate the data over the course of several minutes/seconds
  - Write the data to HDFS
  - Have a CRON job that runs hourly to crunch the data and calculate the total frequency over the hour and its top K heavy hitters
- Serving layer
  - Use a NoSQL database to store the data from speed layer and batch layer
  - If under one hour then aggregate data from speed layer
  - If more than one hour then use data from both speed and batch layer
