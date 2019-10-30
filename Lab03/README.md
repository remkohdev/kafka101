# Lab03 - Produce and Consume Messages to a Kafka Stream with Spring Boot

The Spring Framework supports the following dependencies we will use in a Spring Boot application to produce and consume Kafka Streams:

* `web` to build web, including RESTful, applications using Spring MVC, uses Apache Tomcat as the default embedded container.
* `data-rest` to expose Spring Data repositories over REST via Spring Data REST. 
* `kafka` to publish, subscribe, store, and process streams of records.
* `kafka-streams` to build stream processing applications with Apache Kafka Streams.

If you have the Spring Boot CLI already installed, skip to the "Create a Spring App" section.

* On Mac, install the Spring Boot CLI using Homebrew,

	```console
	$ brew tap pivotal/tap 
	$ brew install springboot
	```

## Create a Spring App

* Create a new Spring Boot application with dependencies for web, data-rest, kafka, and kafka-streams.

	```console
	$ spring init --dependencies=web,data-rest,kafka,kafka-streams spring-boot-kafka-app
	$ cd spring-boot-kafka-app/
	```

* To see a complete list of supported dependencies,

	```console
	$ spring init --list
	```

* Edit the file src/main/resources/application.properties, 

    ```console
    $ vi src/main/resources/application.properties
    ```

* Press the 'i' key to enable INSERT mode,
* Add the following common Spring Boot properties to configure Kafka,
* Replace <password> with the password from the Event Streams credentials,
* Replace <brokerlist> with the 'kafka_brokers_sasl' from the Event Streams credentials, remove the quotes and newlines,

	```text
	# Spring server config
	server.port=8080
	spring.application.name=spring-boot-kafka-app

	# Kafka connection
	spring.kafka.jaas.enabled=true
	spring.kafka.jaas.login-module=org.apache.kafka.common.security.plain.PlainLoginModule
	spring.kafka.jaas.options.username=token
	spring.kafka.jaas.options.password=<password>
	spring.kafka.bootstrap-servers=<brokerlist>
	spring.kafka.properties.security.protocol=SASL_SSL
	spring.kafka.properties.sasl.mechanism=PLAIN
	spring.kafka.ssl.protocol=TLSv1.2

	# Kafka Producer
	spring.kafka.template.default-topic=greetings
	spring.kafka.producer.client-id=event-streams-kafka
	spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
	spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer

	# Kafka Consumer
	listener.topic=greetings
	spring.kafka.consumer.group-id=channel1
	spring.kafka.consumer.auto-offset-reset=earliest
	spring.kafka.consumer.key-deserializer=org.apache.kafka.common.serialization.StringDeserializer
	spring.kafka.consumer.value-deserializer=org.apache.kafka.common.serialization.StringDeserializer
	```

* Press the ESC key to exit the INSERT mode, and type ':wq' to write and quit 'vi',

* The Kafka connection properties above can be configured using the Service credentials created for the IBM Event Streams service on IBM Cloud. Go to the dashboard of the IBM Event Streams service, go to the Service credentials page, and click View credentials,

	```json
	{
	"api_key": "1abCDEFgHi2jKlmnO3pqrsTU4VwXyzaBcdeFgHiJkLmN",
	"apikey": "1abCDEFgHi2jKlmnO3pqrsTU4VwXyzaBcdeFgHiJkLmN",
	"iam_apikey_description": "Auto-generated for key 1abCDEFgHi2jKlmnO3pqrsTU4VwXyzaBcdeFgHiJkLmN",
	"iam_apikey_name": "remkohdev-eventstreams-kafka-servicecredentials-1",
	"iam_role_crn": "crn:v1:bluemix:public:iam::::serviceRole:Manager",
	"iam_serviceid_crn": "crn:v1:bluemix:public:iam-identity::a/12345a678b9012cd3e456fg78h9i012j::serviceid:ServiceId-1234a567-89bc-01d2-ef34-g5678h90123i",
	"instance_id": "1234a567-89bc-01d2-ef34-g5678h90123i",
	"kafka_admin_url": "https://a1bc2d3efg4hijkl.svc01.us-south.eventstreams.cloud.ibm.com",
	"kafka_brokers_sasl": [
		"broker-1-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999",
		"broker-2-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999",
		"broker-3-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999",
		"broker-4-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999",
		"broker-5-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999",
		"broker-6-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999"
	],
	"kafka_http_url": "https://a1bc2d3efg4hijkl.svc01.us-south.eventstreams.cloud.ibm.com",
	"password": "1abCDEFgHi2jKlmnO3pqrsTU4VwXyzaBcdeFgHiJkLmN",
	"user": "token"
	}
	```

* The bootstrap-servers in the properties are set to the 'kafka_brokers_sasl' in the credentials. 
* Add a new file src/main/java/com/example/springbootkafkaapp/EventsStreamController.java,

    ```console
    $ vi src/main/java/com/example/springbootkafkaapp/EventsStreamController.java
    ```
  
* Press the 'i' key to enable INSERT mode,
* Add the following code,

	```java
	package com.example.springbootkafkaapp;
	
	import org.apache.kafka.clients.consumer.ConsumerRecord;
	import org.slf4j.Logger;
	import org.slf4j.LoggerFactory;
	import org.springframework.beans.factory.annotation.Autowired;
	import org.springframework.kafka.annotation.KafkaListener;
	import org.springframework.kafka.core.KafkaTemplate;
	import org.springframework.web.bind.annotation.GetMapping;
	import org.springframework.web.bind.annotation.PathVariable;
	import org.springframework.web.bind.annotation.RestController;

	import java.util.List;
	import java.util.concurrent.CopyOnWriteArrayList;

	@RestController
	public class EventsStreamController {
		
		@Autowired
		private KafkaTemplate<String, String> template;
		
		private List<String> messages = new CopyOnWriteArrayList<>();

		@KafkaListener(topics = "${listener.topic}", groupId = "channel1")
		public void listen(ConsumerRecord<String, String> cr) throws Exception {
			messages.add(cr.value());
		}

		@GetMapping(value = "/send/{msg}")
		public void send(@PathVariable String msg) throws Exception {
			this.template.sendDefault(msg);
		}

		@GetMapping("/received")
		public String recv() throws Exception {
			String result = messages.toString();
			messages.clear();
			return result;
		}
	}
	```

* Press the ESC key to exit the INSERT mode, and type ':wq' to write and quit 'vi',

* For the original code example see the Spring Boot guide's an Even Quicker with Spring Boot example.
* Clean, Install and Run the application,

	```console
	$ mvn clean install
	$ mvn spring-boot:run
	```

* Open a new terminal window,
* Test the Spring Boot Kafka client and the IBM Event Streams connection,

	```console
	$ curl -X GET http://localhost:8080/send/Hello1
	$ curl -X GET http://localhost:8080/received
	[Hello1]
	```
