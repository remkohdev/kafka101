# Lab05 - Using the Event Streams CLI Plugin

* List the Event Streams CLI Plugin commands,

	```console
	$ ibmcloud es -h
	NAME:
	ibmcloud es - Plugin for IBM Event Streams (build 1908221834)

	USAGE:
	ibmcloud es command [arguments...] [command options]

	COMMANDS:
	----------------------------------------------------------------------------------------------------------------------------------------

	broker                 Display details of a broker.
	broker-config          Display broker configuration.
	cluster                Display details of the cluster.
	group                  Display details of a consumer group.
	group-delete           Delete a consumer group.
	group-reset            Reset the offsets for a consumer group.
	groups                 List the consumer groups.
	init                   Initialize the IBM Event Streams plugin.
	topic                  Display details of a topic.
	topic-create           Create a new topic.
	topic-delete           Delete a topic.
	topic-delete-records   Delete records from a topic before a given offset.
	topic-partitions-set   Set the partitions for a topic.
	topic-update           Update the configuration for a topic.
	topics                 List the topics.
	help, h                Show help

	```

* Create an instance of the IBM Event Streams service,

	```console
	$ ibmcloud resource service-instance-create user1-eventstreams messagehub standard us-south
	Creating service instance remkohdev-eventstreams in resource group default of account USER1's Account as user1@email.com...
	OK
	Service instance user1-eventstreams was created.
					
	Name:         user1-eventstreams   
	ID:           crn:v1:bluemix:public:messagehub:us-south:a/1ab2c3de456789fg01h23i4j5k6l78mn:12a34bc5-de67-8f9g-h012-34i567jk8901::   
	GUID:         12a34bc5-de67-8f9g-h012-34i567jk8901   
	Location:     us-south   
	State:        active   
	Type:         service_instance   
	Sub Type:        
	Created at:   2019-10-17T15:04:16Z   
	Updated at:   2019-10-17T15:04:16Z
	```

* Initialize the Event Streams CLI Plugin,

	```console
	$ ibmcloud es init
	API Endpoint: 	https://123abc4d5efgh67i.svc01.us-south.eventstreams.cloud.ibm.com
	OK
	```

* Create a new topic called `greetings`,

	```console
	$ ibmcloud es topic-create greetings --partitions 1
	Created topic greetings
	OK
	```

* Display details of a topic called `greetings`,

	```console
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
 
* Delete an existing topic called `greetings`,

	```console
	$ ibmcloud es topic-delete greetings
	Really delete topic 'greetings'? [y/N]> y
	Topic greetings deleted successfully
	OK
	```

* List all topics,

	```console
	$ ibmcloud es topics
	OK
	No topics found.
	```

* List all consumer groups,

	```console
	$ ibmcloud es groups
	OK
	No consumer groups found.
	```