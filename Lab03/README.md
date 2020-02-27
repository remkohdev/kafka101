# Lab03 - Produce and Consume Messages to a Kafka Stream with Spring Boot

The Spring Framework supports the following dependencies we will use in a Spring Boot application to produce and consume Kafka Streams:

* `web` to build web, including RESTful, applications using Spring MVC, uses Apache Tomcat as the default embedded container.
* `data-rest` to expose Spring Data repositories over REST via Spring Data REST. 
* `kafka` to publish, subscribe, store, and process streams of records.
* `kafka-streams` to build stream processing applications with Apache Kafka Streams.

The Spring Boot CLI should already be installed in your web-terminal. Do not remove the directory `/userdata/spring-2.2.0.RELEASE` because the $PATH includes the `/userdata/spring-2.2.0.RELEASE/bin` directory that contains the `spring` or Spring Boot CLI.

## Create a Spring App

* Go back to the `/userdata` root,

	```shell
	$ cd /userdata
	```

* To see a complete list of supported dependencies,

	```shell
	$ spring init --list
	```

* Create a new Spring Boot application with dependencies for web, data-rest, kafka, and kafka-streams.

	```shell
	$ spring init --dependencies=web,data-rest,kafka,kafka-streams spring-boot-kafka-app
	$ cd spring-boot-kafka-app/
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

* List the Event Streams service credentials again,

	```shell
	$ ibmcloud resource service-key "${ES_SVC_NAME}credentials1"
	```

* Copy the list of `kafka_brokers_sasl` WITHOUT the square brackets and replace the space separator by a commas,
* Edit the file src/main/resources/application.properties, 

    ```console
    $ vi src/main/resources/application.properties
    ```

* Press the 'i' key to enable INSERT mode,
* Replace the '<brokerlist>' by the value of the list of `kafka_brokers_sasl` WITHOUT the square brackets,
* Press the ESC key to exit the INSERT mode, and type ':wq' to write and quit 'vi',
* Copy the password value from the service credentials,
* Edit the file src/main/resources/application.properties, 

    ```console
    $ vi src/main/resources/application.properties
    ```:q

* Press the 'i' key to enable INSERT mode,
* Replace the '<password>' by the `password` value from the service credentials,
* Press the ESC key to exit the INSERT mode, and type ':wq' to write and quit 'vi',


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

* Open a new web-terminal in a new browser tab,
* Test the Spring Boot Kafka client and the IBM Event Streams connection,
* Note that the first time you call the /received endpoint, this `new consumer` will retrieve ALL previous messages. This will have `cleaned` the stream for this consumer, and you can repeat the /send and /received calls, if you want.

	```console
	$ curl -X GET http://localhost:8080/send/Hello1
	$ curl -X GET http://localhost:8080/received
	[Hello1]
	```

Go to [Lab04](../Lab04).