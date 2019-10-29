# Produce and Consume Streams with Kafka Console Tools

See also https://ibm.github.io/event-streams/tutorials/kafka-streams-app/

The Apache Kafka console tools ship with the Apache Kafka distribution and can be found in the bin directory of the Kafka download. 

* Go to http://kafka.apache.org/downloads
* Download the Source Download, e.g. version 2.3.0
* Unzip the source code to your working directory, e.g. /data
* Browse to the bin directory

	```console
	$ tar -xvzf kafka-2.3.0-src.tgz
	$ cd kafka-2.3.0-src/bin
	```

* Create a new properties file called mykafka.properties, and for USER and PASSWORD use the values from the Event Streams service credentials,
  

	```text
	sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="USER" password="PASSWORD";
	security.protocol=SASL_SSL
	sasl.mechanism=PLAIN
	ssl.protocol=TLSv1.2
	ssl.enabled.protocols=TLSv1.2
	ssl.endpoint.identification.algorithm=HTTPS
	```

* First, run the producer, for --broker-list use the kafka_brokers_sasl list from the Event Streams service credentials,

	```console
	$ bash kafka-console-producer.sh --broker-list broker-1-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999,
	broker-2-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999,
	broker-3-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999,
	broker-4-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999,
	broker-5-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999,
	broker-6-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999
	--producer.config mykafka.properties --topic greetings
	SLF4J: Class path contains multiple SLF4J bindings.
	SLF4J: Found binding in [jar:file:/data/kafka-2.3.0-src/core/build/dependant-libs-2.12.8/slf4j-log4j12-1.7.26.jar!/org/slf4j/impl/StaticLoggerBinder.class]
	SLF4J: Found binding in [jar:file:/data/kafka-2.3.0-src/tools/build/dependant-libs-2.12.8/slf4j-log4j12-1.7.26.jar!/org/slf4j/impl/StaticLoggerBinder.class]
	SLF4J: Found binding in [jar:file:/data/kafka-2.3.0-src/connect/api/build/dependant-libs/slf4j-log4j12-1.7.26.jar!/org/slf4j/impl/StaticLoggerBinder.class]
	SLF4J: Found binding in [jar:file:/data/kafka-2.3.0-src/connect/transforms/build/dependant-libs/slf4j-log4j12-1.7.26.jar!/org/slf4j/impl/StaticLoggerBinder.class]
	SLF4J: Found binding in [jar:file:/data/kafka-2.3.0-src/connect/runtime/build/dependant-libs/slf4j-log4j12-1.7.26.jar!/org/slf4j/impl/StaticLoggerBinder.class]
	SLF4J: Found binding in [jar:file:/data/kafka-2.3.0-src/connect/file/build/dependant-libs/slf4j-log4j12-1.7.26.jar!/org/slf4j/impl/StaticLoggerBinder.class]
	SLF4J: Found binding in [jar:file:/data/kafka-2.3.0-src/connect/json/build/dependant-libs/slf4j-log4j12-1.7.26.jar!/org/slf4j/impl/StaticLoggerBinder.class]
	SLF4J: Found binding in [jar:file:/data/kafka-2.3.0-src/connect/basic-auth-extension/build/dependant-libs/slf4j-log4j12-1.7.26.jar!/org/slf4j/impl/StaticLoggerBinder.class]
	SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
	SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
	>
	```

* Note, the prompt that is displayed on the last line after the log output for loading the producer, once the consumer is running, you can enter messages to publish to the Kafka event stream that is consumed in real time,
* Next, run the consume, for --broker-list use the kafka_brokers_sasl list from the Event Streams service credentials,

	```console
	$ bash kafka-console-consumer.sh --bootstrap-server broker-1-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999,
	broker-2-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999,
	broker-3-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999,
	broker-4-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999,
	broker-5-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999,
	broker-6-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999 
	--consumer.config ../mykafka.properties --topic greetings
	```

* Now that the producer and consumer are both running, publish a message to the event stream, by entering text on the prompt,

	```console
	> hello1
	> hello2
	```

* The consumer listener should immediately pick up the messages from the event stream with topic greetings, 

	```console
	hello1
	hello2
	```

## Consumer Groups

Consumers can be labeled with a consumer group name, so that each record published to a topic is delivered to one consumer instance within a subscribing consumer group,

* Add a --group 1 flag to label a consumer,

	```console
	$ bash kafka-console-consumer.sh --bootstrap-server broker-1-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999,
	broker-2-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999,
	broker-3-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999,
	broker-4-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999,
	broker-5-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999,
	broker-6-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999 
	--consumer.config ../mykafka.properties --topic greetings --group 1
	```

* Run the same command in a new terminal tab, to create a second consumer with the same label --group 1,
* Publish messages to the topic greetings,
* You will see 1 consumer in the group consume each message,
