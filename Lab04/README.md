# Lab04 - Using the Admin API

Retrieve the Event Streams credentials,

```
$ ibmcloud resource service-key "${ES_SVC_NAME}credentials1"
```

To access the Kafka Admin API, use the value for property kafka_admin_url.

```
# KAFKA_ADMIN_URL=https://abcdef1g2hi3456jk.svc02.us-south.eventstreams.cloud.ibm.com 
```

The Swagger 2.0 docs for the IBM Event Streams Administration REST API is found at https://raw.githubusercontent.com/ibm-messaging/event-streams-docs/master/admin-rest-api/admin-rest-api.yaml. You can load the file via URL import in the http://editor.swagger.io, click File > Import URL > OK. Or read the README for the Admin API at https://github.com/ibm-messaging/event-streams-docs/tree/master/admin-rest-api.

To authenticate add an X-Auth-Token HTTP Header with the apikey found in the service credentials.

```
$ KAFKA_APIKEY=abCDEfGh12ijklm3noPQRsTUacEwP6TgbJ5uoLMrof3N
```

To use the Admin API you need these full `kafka_admin_url` and the `apikey` from the `Service Credentials`. In the examples below, replace the placeholders for these properties with the values from the `Service Credentials` of your Event Streams service,

* List the Kafka topics,

	```
	$ curl -X GET "${KAFKA_ADMIN_URL}/admin/topics" -H "X-Auth-Token: ${KAFKA_APIKEY}"
	```

	will return

	```
	[
		{
			"name": "greetings",
			"partitions": 1,
			"replicationFactor": 3,
			"retentionMs": 86400000,
			"cleanupPolicy": "delete",
			"configs": {
				"cleanup.policy": "delete",
				"min.insync.replicas": "2",
				"retention.bytes": "104857600",
				"retention.ms": "86400000",
				"segment.bytes": "536870912"
			},
			"replicaAssignments": [
				{
					"id": 0,
					"brokers": {
						"replicas": [
							0,
							1,
							2
						]
					}
				}
			]
		}
	]
	```

* Delete a topic,
  
	```console
	$ curl -X DELETE "${KAFKA_ADMIN_URL}/admin/topics/greetings" -H "X-Auth-Token: ${KAFKA_APIKEY}"
	```

* Create a topic,
	```
	$ curl -X POST \
	"${KAFKA_ADMIN_URL}/admin/topics" \
	-H "X-Auth-Token: ${KAFKA_APIKEY}" \
	-d '{
	"name": "greetings",
	"partitions": 1
	}'
	```

* Get a topic,

	```console
	$ curl -X GET \
	"${KAFKA_ADMIN_URL}/admin/topics/greetings" \
	-H "X-Auth-Token: ${KAFKA_APIKEY}"
	```

* Update a topic configuration, fyi this example will return with 'invalid for topic'...

	```console
	$ curl -X PATCH \
	"${KAFKA_ADMIN_URL}/admin/topics/greetings" \
	-H "X-Auth-Token: ${KAFKA_APIKEY}" \
	-d '{
	"new_total_partition_count": 1
	}'
	```

Go to [Lab05](../Lab05).