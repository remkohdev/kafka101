# Setup

For this lab you need a managed instance of Apache Kafka on IBM Cloud. We already created an IBM Event Streams Service on IBM Cloud for you. An 

IBM Event Streams is a managed service of an Apache Kafka instance on IBM Cloud. IBM Event Streams is a high-throughput message bus built on Apache Kafka, supporting all Kafka APIs and optimized for event ingestion and event stream distribution on IBM Cloud.

If you already have installed the Event Streams plugin and are logged into IBM Cloud, jump to "Initialize the Event Streams Service".

## Install the Event Streams plugin

The IBM Cloud Developer Tools CLI and the plugin for Event Streams are pre-installed in your web-terminal container,

List the plugins,

```shell
$ ibmcloud plugin list
Listing installed plug-ins...

Plugin Name                            Version   Status   
cloud-functions/wsk/functions/fn       1.0.35       
cloud-object-storage                   1.1.0        
container-registry                     0.1.437      
container-service/kubernetes-service   0.4.42       
dev                                    2.4.0        
event-streams                          2.0.0 
```

If for some reason, the Event Streams plugin is not listed, install it as follow,

```shell
$ ibmcloud plugin install event-streams
Looking up 'event-streams' from repository 'IBM Cloud'...
Plug-in 'event-streams 2.0.0' found in repository 'IBM Cloud'
Attempting to download the binary file...
30.95 MiB / 30.95 MiB [===========================================] 100.00% 17s
32455568 bytes downloaded
Installing binary...
OK
Plug-in 'event-streams 2.0.0' was successfully installed into /Users/user1/.bluemix/plugins/event-streams. 
Use 'ibmcloud plugin show event-streams' to show its details.
```

### Initialize the Event Streams Service

In order to initialize the Event Streams plugin and connect to your Event Streams Service instance, you must be logged in to IBM Cloud.

Check that you are logged in and connected to the IBM Cloud.

```shell
$ ibmcloud target
```

You should see the targeted region, account, resource group, org and space of your connection.

Then initialize the Event Streams plugin to connect to your Event Streams Service instance. Use the `Event Streams Service Name` that you were assigned and given for this workshop. If you followed the Get Started instructions in the AppModernization/lab2 repository, you should have set an environment variable `ES_SVC_NAME`.

```shell
$ echo $ES_SVC_NAME
```

If no Event Streams Service name is returned, set it now, e.g.

```shell
$ ES_SVC_NAME=<accountname>esuser<user number>
```

Initialize the Event Streams Service plugin,

```console
$ ibmcloud es init -i $ES_SVC_NAME

API Endpoint: 	https://123abc4d5efgh67i.svc01.us-south.eventstreams.cloud.ibm.com
OK
```

## Using the IBM Cloud Developer Tools CLI and Event Streams plugin

* List all topics,

	```shell
	$ ibmcloud es topics
	OK
	No topics found.
	```
	
* Create a new topic called `greetings` with 1 partition,

	```console
	$ ibmcloud es topic-create greetings --partitions 1
	Created topic greetings
	OK
	```

* It may take a little while before the topic is created,

* Display details of the topic called `greetings`,

	```shell
	$ ibmcloud es topic greetings
	Details for topic greetings
	Topic name   Internal?   Partition count   Replication factor   
	greetings    false       1                 3   

	Partition details for topic greetings
	Partition ID   Leader   Replicas   In-sync   
	0              2        [2 0 1]    [2 0 1]   

	Configuration parameters for topic greetings
	Name                  Value   
	cleanup.policy        delete   
	min.insync.replicas   2   
	segment.bytes         536870912   
	retention.ms          86400000   
	retention.bytes       104857600   
	OK
	```

For more details about using the Event Streams CLI Plugin, see [Lab05](../Lab05/README.md).

Continue to [Lab02](../Lab02).


