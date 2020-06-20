# helm-kafka-connect
Helm chart enable you to deploy Confluent Connect and schemaRegistry services on Kubernetes.

## Configuration

The following table lists the configurable parameters of the Kafka Connect and schema Registry chart and their default values.

| Parameter         | Description                           | Default   |
| ----------------- | ------------------------------------- | --------- |
| `KafkaBootstrapServers` | Bootstrap Servers for Kafka connect and Schema Registry if enabled  | `""` |
| `connect.replicaCount`    | The number of Kafka Connect Servers.  | `1`       |
| `connect.image` | Docker Image of Confluent Kafka Connect. | `confluentinc/cp-kafka-connect` |
| `connect.imageTag` | Docker Image Tag of Confluent Kafka Connect. | `5.5.0` |
| `connect.imagePullPolicy` | Docker Image Tag of Confluent Kafka Connect. | `IfNotPresent` |
| `connect.servicePort` | The port on which the Kafka Connect will be available and serving requests. | `8083` |
| `connect.configurationOverrides` | Kafka Connect [configuration](https://docs.confluent.io/current/connect/references/allconfigs.html) overrides in the dictionary format. | `{}` |
| `connect.customEnv` | Custom environmental variables | `{}` |
| `connect.heapOptions` | The JVM Heap Options for Kafka Connect | `"-Xms512M -Xmx512M"` |
| `connect.podAnnotations` | Map of custom annotations to attach to the pod spec. | `{}` |
`connect.health.readinessProbe` | Readiness probe configurations | `{ "httpGet": { "path": "/connectors", "port": "kafka-connect" } }`
`connect.service.type` | Kafka-connect service type | `ClusterIP`
`connect.service.ports.targetPort` | Kafka-connect service port name | `http`
`connect.service.annotations` | Optional service annotations | `{}`
`connect.resources` | Pod resource requests and limits | `{}`
`connect.nodeSelector` | Node labels for pod assignment | `{}`
`connect.tolerations` | Tolerations for pod assignment | `[]`
`connect.antiAffinity` | Affinity for pod assignment | `{}`
`ingress.enabled` | If true, create an ingress resource | `false`
`ingress.annotations` | Optional ingress annotations | `{}`
`ingress.path` | Ingress path | `/`
`ingress.hosts` | Ingress hostnames | `kafka-connect.local`
`schemaRegistry.enabled` | If true, deploy Schema registery | `true`
| `schemaRegistry.replicaCount` | The number of Schema Registry Servers. | `1` |
| `schemaRegistry.image` | Docker Image of Confluent Schema Registry. | `confluentinc/cp-schema-registry` |
| `schemaRegistry.imageTag` | Docker Image Tag of Confluent Schema Registry. | `5.5.0` |
| `schemaRegistry.imagePullPolicy` | Docker Image Tag of Confluent Schema Registry. | `IfNotPresent` |
| `schemaRegistry.configurationOverrides` | Schema Registry [configuration](https://docs.confluent.io/current/schema-registry/docs/config.html) overrides in the dictionary format. | `{}` |
| `schemaRegistry.customEnv` | Custom environmental variables | `{}` |
| `schemaRegistry.servicePort` | The port on which the Schema Registry will be available and serving requests. | `8081` |
| `schemaRegistry.podAnnotations` | Map of custom annotations to attach to the pod spec. | `{}` |
| `schemaRegistry.nodeSelector` | Dictionary containing key-value-pairs to match labels on nodes. When defined pods will only be scheduled on nodes, that have each of the indicated key-value pairs as labels. Further information can be found in the [Kubernetes documentation](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) | `{}`
| `schemaRegistry.tolerations`| Array containing taint references. When defined, pods can run on nodes, which would otherwise deny scheduling. Further information can be found in the [Kubernetes documentation](https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/) | `{}`

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

## Install & Upgrade
To add the chart for your local client, run helm repo add:

```sh 
$ helm repo add myrepo https://mahagamal.github.io/helm-kafka-connect/
     "myrepo" has been added to your repositories 
```
You can then run `helm search myrepo` to see the charts.
```sh
$ helm install --name mychart --namespace <ns> myrepo/kafka-connect --set KafkaBootstrapServers=??
```
```sh
$ helm upgrade <ns>-<CHARTNAME> --install --namespace <ns> myrepo/kafka-connect [--version CHART-VERSION] --debug
```

## Connect's REST API
Since Kafka Connect is intended to be run as a service, it also supports a REST API for managing connectors. By default this service runs on port `8083`. The following are the currently supported endpoints:

| API                    | description                       |
|:----------------------------|:----------------------------------|
| `GET /connectors`     | return a list of active connectors |
| `POST /connectors`    | create a new connector; the request body should be a JSON object containing a string name field and a object config field with the connector configuration parameters |
| `GET /connectors/{name}`| get information about a specific connector |
| `GET /connectors/{name}/config` |  get the configuration parameters for a specific connector |
| `PUT /connectors/{name}/config`   | update the configuration parameters for a specific connector |
| `GET /connectors/{name}/tasks`   |  get a list of tasks currently running for a connector |
| `DELETE /connectors/{name}` | delete a connector, halting all tasks and deleting its configuration |
