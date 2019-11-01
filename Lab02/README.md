# Produce and Consume Streams with Kafka Console Tools

See also https://ibm.github.io/event-streams/tutorials/kafka-streams-app/

The Apache Kafka console tools ship with the Apache Kafka distribution and can be found in the bin directory of the Kafka download. If you have already downloaded Kafka, skip to the Lab section.

The Source Download, e.g. version 2.3.0, has been already downloaded and installed into your working directory `/userdata`.

* Go to the Kafka distribution directory,

	```console
	$ cd /userdata
	$ ls -al 
	total 16
	drwxr-xr-x 1 root root 4096 Oct 29 19:43 .
	drwxr-xr-x 1 root root 4096 Oct 29 22:12 ..
	drwxr-xr-x 6 root root 4096 Oct  1 16:45 istio-1.3.2
	drwxr-xr-x 6 root root 4096 Jun 19 20:44 kafka_2.12-2.3.0
	$ cd kafka_2.12-2.3.0
	```

* You will create a Kafka configuration file first. But we need the Kafka service credentials to set the Kafka configuration,

	```shell
	$ ibmcloud resource service-key "${ES_SVC_NAME}-credentials1"
	Retrieving service key account-eventstreams-user8888-credentials1 in resource group workshop-nov2019 under account Account as me@email.com...
                  
Name:          account-eventstreams-user8888-credentials1   
ID:            crn:v1:bluemix:public:messagehub:us-south:a/accf1c23dd456789befedcd0cda123e4:56ce78aa-d9a0-1c23-34ce-5a6cf7bd8d90:resource-key:1fe2ad34-5678-90fe-12d3-d4567d890c12   
Created At:    Thu Oct 31 03:18:44 UTC 2019   
State:         active   
Credentials:                                   
               api_key:                  someapikey      
               apikey:                   someapikey      
               iam_apikey_description:   Auto-generated for key someapikey     
               iam_apikey_name:          account-eventstreams-user8888-credentials1      
               iam_role_crn:             crn:v1:bluemix:public:iam::::serviceRole:Manager      
               iam_serviceid_crn:        crn:v1:bluemix:public:iam-identity::a/accf1c23dd456789befedcd0cda123e4::serviceid:ServiceId-123456789      
               instance_id:              56ce78aa-d9a0-1c23-34ce-5a6cf7bd8d90      
               kafka_admin_url:          https://a12bcdefg3hij45.svc01.us-south.eventstreams.cloud.ibm.com      
               kafka_brokers_sasl:       [broker-1-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams. cloud.ibm.com:9999,
				broker-2-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999,
				broker-3-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999,
				broker-4-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999,
				broker-5-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999,
				broker-6-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999]      
               kafka_http_url:           https://a12bcdefg3hij45.svc01.us-south.eventstreams.cloud.ibm.com      
               password:                 123abc4567sdagdh2678akd7890hh      
               user:                     token 
	```

* First thing, for later use, create an environment variable $KAFKA_BROKERS_SASL and set it to the complete Array value, including the square brackets and enclosed by double quotes, of the `kafka_brokers_sasl` property in the service credentials,

	```shell
	$ KAFKA_BROKERS_SASL="[broker-1-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams. cloud.ibm.com:9999,broker-2-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999, broker-3-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999, broker-4-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999, broker-5-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999, broker-6-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999]"
	```

* Next, we need the `username` (always set to `token`) and `password` of your Kafka or Event Streams instance,
* Look for the `password` value. You need to set the `password` property in the `mykafka.properties` file with this value,
* Create a new properties file called 'mykafka.properties', 

    ```console
    $ vi mykafka.properties
    ```

* Press the 'i' key to enable INSERT mode,
* Copy-paste the following properties, user 'token' for 'username' and for 'PASSWORD' use the 'password' value from the Event Streams service credentials,

```text
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="token" password="PASSWORD";
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
ssl.protocol=TLSv1.2
ssl.enabled.protocols=TLSv1.2
ssl.endpoint.identification.algorithm=HTTPS
```

* Press the ESC key to exit INSERT mode, and :wq to write and quit vi,
* In your terminal you should still see the password value of the service credentials. Copy this value, and go back to the `vi` editor of the `mykafka.properties`,

    ```console
    $ vi mykafka.properties
    ```

* Set the password property in the first line,
* Press the ESC key to exit INSERT mode, and :wq to write and quit vi,

Now you are ready to run the producer and publish messages to the Kafka messages stream:
* First, run the producer, 

	```console
	$ bash bin/kafka-console-producer.sh --broker-list $KAFKA_BROKERS_SASL 
	--producer.config mykafka.properties --topic greetings
	>
	```

* Note the prompt that should be displayed on the last line after the log output for loading the producer, once the consumer is running, you can enter messages to publish to the Kafka event stream that is consumed in real time,
* Lets place messages on the event stream, by entering messages at the prompt. Hit ENTER to send and start a new message.

	```shell
	> hello1
	> hello2
	> hello3
	> we could go on forever and ever and ever
	```

* Hit CTRL-C to stop the producer,
* Next, run the consumer, for --broker-list use the kafka_brokers_sasl list from the Event Streams service credentials, as you did with the producer,

	```console
	$ bash bin/kafka-console-consumer.sh --bootstrap-server $KAFKA_BROKERS_SASL --consumer.config mykafka.properties --topic greetings --from-beginning
	```

* The consumer listener should retrieve the messages from the event stream with topic greetings, 

	```console
	hello1
	hello2
	hello3
	we could go on forever and ever and ever
	```
* Hit CTRL-C to stop the consumer,


## Consumer Groups

Consumers can be labeled with a consumer group name, so that each record published to a topic is delivered to one consumer instance within a subscribing consumer group,

* Keep your current browser tab and terminal open, do not close it,
* Echo the $KAFKA_BROKERS_SASL

	```shell
	$ echo $KAFKA_BROKERS_SASL
	[broker-1-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams. cloud.ibm.com:9999,broker-2-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999, broker-3-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999, broker-4-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999, broker-5-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999, broker-6-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999]
	```

* Copy the list to the clipboard,
* Open 3 new browser tabs and run your terminal in each,
* You lost the context of your original terminal, so we need to set the $KAFKA_BROKERS_SASL variable again in all 3 new terminals,
* In each new web-terminal,

	```shell
	$ cd kafka_2.12-2.3.0
	$ KAFKA_BROKERS_SASL="[broker-1-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams. cloud.ibm.com:9999,broker-2-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999, broker-3-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999, broker-4-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999, broker-5-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999, broker-6-a1bc2d3efg4hijkl.kafka.svc01.us-south.eventstreams.cloud.ibm.com:9999]"
	```

* In the first 2 new terminals, run a consumer instance that is a member of group 1, add a --group 1 flag to label a consumer,

	```console
	$ bash bin/kafka-console-consumer.sh --bootstrap-server $KAFKA_BROKERS_SASL --consumer.config mykafka.properties --topic greetings --group 1
	```

* In the last, third new terminal, run a consumer instance that is a member of group 2,
* Add a --group 2 flag to label a consumer,

	```console
	$ bash bin/kafka-console-consumer.sh --bootstrap-server $KAFKA_BROKERS_SASL --consumer.config mykafka.properties --topic greetings --group 2
	```

* Go back to the original terminal that is still open, 
* Run the producer,

	```shell
	$ bash bin/kafka-console-producer.sh --broker-list $KAFKA_BROKERS_SASL --producer.config mykafka.properties --topic greetings
	>
	```
* Publish a new message to the topic greetings,

	```shell
	> listen carefully, i will say this only once
	```

* Look at the 3 consumers in the 3 new web-terminal tabs,
* You should see that only 1 consumer in group 1 and the single consumer in group 2, consume the message once per group,

	```shell
	listen carefully, i will say this only once
	```

	