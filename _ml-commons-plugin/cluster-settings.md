---
layout: default
title: ML Commons cluster settings
has_children: false
nav_order: 160
---

# ML Commons cluster settings

To enhance and customize your OpenSearch cluster for machine learning (ML), you can add and modify several configuration settings for the ML Commons plugin in your 'opensearch.yml' file.


## Run tasks and models on ML nodes only

If `true`, ML Commons tasks and models run machine learning (ML) tasks on ML nodes only. If `false`, tasks and models run on ML nodes first. If no ML nodes exist, tasks and models run on data nodes. We recommend that you do not set this value to "false" on production clusters. 

### Setting

```
plugins.ml_commons.only_run_on_ml_node: true
```

### Values

- Default value: `true`
- Value range: `true` or `false`

## Dispatch tasks to ML node 

`round_robin` dispatches ML tasks to ML nodes using round robin routing. `least_load` gathers runtime information from all ML nodes, like JVM heap memory usage and running tasks, and then dispatches the tasks to the ML node with the lowest load.


### Setting

```
plugins.ml_commons.task_dispatch_policy: round_robin
```


### Values

- Default value: `round_robin`
- Value range: `round_robin` or `least_load`

## Set number of ML tasks per node

Sets the number of ML tasks that can run on each ML node. When set to `0`, no ML tasks run on any nodes.

### Setting

```
plugins.ml_commons.max_ml_task_per_node: 10
```

### Values

- Default value: `10`
- Value range: [0, 10,000]

## Set number of ML models per node

Sets the number of ML models that can be deployed to each ML node. When set to `0`, no ML models can deploy on any node.

### Setting

```
plugins.ml_commons.max_model_on_node: 10
```

### Values

- Default value: `10`
- Value range: [0, 10,000]

## Set sync job intervals 

When returning runtime information with the [Profile API]({{site.url}}{{site.baseurl}}/ml-commons-plugin/api#profile), ML Commons will run a regular job to sync newly deployed or undeployed models on each node. When set to `0`, ML Commons immediately stops sync-up jobs.


### Setting

```
plugins.ml_commons.sync_up_job_interval_in_seconds: 3
```

### Values

- Default value: `3`
- Value range: [0, 86,400]

## Predict monitoring requests

Controls how many predict requests are monitored on one node. If set to `0`, OpenSearch clears all monitoring predict requests in cache and does not monitor for new predict requests.

### Setting

```
plugins.ml_commons.monitoring_request_count: 100
```

### Value range

- Default value: `100`
- Value range: [0, 10,000,000]

## Upload model tasks per node

Controls how many upload model tasks can run in parallel on one node. If set to `0`, you cannot upload models to any node.

### Setting

```
plugins.ml_commons.max_upload_model_tasks_per_node: 10
```


### Values 

- Default value: `10`
- Value range: [0, 10]


## Load model tasks per node

Controls how many load model tasks can run in parallel on one node. If set to 0, you cannot load models to any node.

### Setting

```
plugins.ml_commons.max_load_model_tasks_per_node: 10
```

### Values 

- Default value: `10`
- Value range: [0, 10]

## Add trusted URL

The default value allows you to upload a model file from any http/https/ftp/local file. You can change this value to restrict trusted model URLs.


### Setting

The default URL value for this trusted URL setting is not secure. To ensure the security, please use you own regex string to the trusted repository that contains your models, for example `https://github.com/opensearch-project/ml-commons/blob/2.x/ml-algorithms/src/test/resources/org/opensearch/ml/engine/algorithms/text_embedding/*`.
{: .warning }


```
plugins.ml_commons.trusted_url_regex: <model-repository-url>
```

### Values

- Default value: `"^(https?|ftp|file)://[-a-zA-Z0-9+&@#/%?=~_|!:,.;]*[-a-zA-Z0-9+&@#/%=~_|]"`
- Value range: Java regular expression (regex) string

## Assign task timeout

Assigns how long in seconds an ML task will live. After the timeout, the task will fail.

### Setting

```
plugins.ml_commons.ml_task_timeout_in_seconds: 600
```

### Values

- Default value: 600
- Value range: [1, 86,400]

## Set native memory threshold 

Sets a circuit breaker that checks all system memory usage before running an ML task. If the native memory exceeds the threshold, OpenSearch throws an exception and stops running any ML task. 

Values are based on the percentage of memory available. When set to `0`, no ML tasks will run. When set to `100`, the circuit breaker closes and no threshold exists.

### Setting

```
plugins.ml_commons.native_memory_threshold: 90
```

### Values

- Default value: 90
- Value range: [0, 100]

## Allow custom deployment plans

When enabled, this setting grants users the ability to deploy models to specific ML nodes according to that user's permissions.

### Setting

```
plugins.ml_commons.allow_custom_deployment_plan: false
```

### Values

- Default value: false
- Value range: [false, true]

## Enable auto redeploy

This setting automatically redeploys deployed or partially deployed models upon cluster failure. If all ML nodes inside a cluster crash, the model switches to the `DEPLOYED_FAILED` state, and the model must be deployed manually.

### Setting

```
plugins.ml_commons.model_auto_redeploy.enable: false
```

### Values

- Default value: false
- Value range: [false, true]

## Set retires for auto redeploy

This setting sets the limit for the number of times a deployed or partially deployed model will try and redeploy when ML nodes in a cluster fail or new ML nodes join the cluster.

### Setting

```
plugins.ml_commons.model_auto_redeploy.lifetime_retry_times: 3
```

### Values

- Default value: 3
- Value range: [0, 100]

## Set auto redeploy success ratio

This setting sets the ratio of success for the auto-redeployment of a model based on the available ML nodes in a cluster. For example, if ML nodes crash inside a cluster, the auto redeploy protocol adds another node or retires a crashed node. If the ratio is `0.7` and 70% of all ML nodes successfully redeploy the model on auto-redeploy activation, the redeployment is a success. If the model redeploys on fewer than 70% of available ML nodes, the auto-redeploy retries until the redeployment succeeds or OpenSearch reaches [the maximum number of retries](#set-retires-for-auto-redeploy).

### Setting

```
plugins.ml_commons.model_auto_redeploy_success_ratio: 0.8
```

### Values

- Default value: 0.8
- Value range: [0, 1]
