---
title: cassandra
type: output
status: beta
---

<!--
     THIS FILE IS AUTOGENERATED!

     To make changes please edit the contents of:
     lib/output/cassandra.go
-->

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

BETA: This component is mostly stable but breaking changes could still be made outside of major version releases if a fundamental problem with the component is found.

Runs a query against a Cassandra database for each message in order to insert data.


<Tabs defaultValue="common" values={[
  { label: 'Common', value: 'common', },
  { label: 'Advanced', value: 'advanced', },
]}>

<TabItem value="common">

```yaml
# Common config fields, showing default values
output:
  cassandra:
    args: []
    max_in_flight: 1
    batching:
      count: 0
      byte_size: 0
      period: ""
      check: ""
```

</TabItem>
<TabItem value="advanced">

```yaml
# All config fields, showing default values
output:
  cassandra:
    addresses: []
    password_authenticator:
      enabled: false
      username: ""
      password: ""
    query: ""
    args: []
    consistency: QUORUM
    max_retries: 3
    backoff:
      initial_interval: 1s
      max_interval: 5s
      max_elapsed_time: 30s
    max_in_flight: 1
    batching:
      count: 0
      byte_size: 0
      period: ""
      check: ""
      processors: []
```

</TabItem>
</Tabs>

Query arguments are set using [interpolation functions](/docs/configuration/interpolation#bloblang-queries) in the `args` field.

## Performance

This output benefits from sending multiple messages in flight in parallel for
improved performance. You can tune the max number of in flight messages with the
field `max_in_flight`.

This output benefits from sending messages as a batch for improved performance.
Batches can be formed at both the input and output level. You can find out more
[in this doc](/docs/configuration/batching).

## Examples

<Tabs defaultValue="Insert JSON Documents" values={[
{ label: 'Insert JSON Documents', value: 'Insert JSON Documents', },
]}>

<TabItem value="Insert JSON Documents">

The following example inserts JSON documents into the table `footable` of the keyspace `foospace` using INSERT JSON (https://cassandra.apache.org/doc/latest/cql/json.html#insert-json).

```yaml
output:
  cassandra:
    addresses:
      - localhost:9042
    query: 'INSERT INTO foospace.footable JSON ?'
    args:
      - ${! content() }
    batching:
      count: 500
```

</TabItem>
</Tabs>

## Fields

### `addresses`

A list of Cassandra nodes to connect to. Multiple comma separated addresses can be specified on a single line.


Type: `array`  
Default: `[]`  

```yaml
# Examples

addresses:
  - localhost:9042

addresses:
  - foo:9042
  - bar:9042

addresses:
  - foo:9042,bar:9042
```

### `password_authenticator`

An object containing the username and password.


Type: `object`  

### `password_authenticator.enabled`

Whether to use password authentication.


Type: `bool`  
Default: `false`  

### `password_authenticator.username`

A username.


Type: `string`  
Default: `""`  

### `password_authenticator.password`

A password.


Type: `string`  
Default: `""`  

### `query`

A query to execute for each message.


Type: `string`  
Default: `""`  

### `args`

A list of arguments for the query to be resolved for each message.
This field supports [interpolation functions](/docs/configuration/interpolation#bloblang-queries).


Type: `array`  
Default: `[]`  

### `consistency`

The consistency level to use.


Type: `string`  
Default: `"QUORUM"`  
Options: `ANY`, `ONE`, `TWO`, `THREE`, `QUORUM`, `ALL`, `LOCAL_QUORUM`, `EACH_QUORUM`, `LOCAL_ONE`.

### `max_retries`

The maximum number of retries before giving up on the request. If set to zero there is no discrete limit.


Type: `number`  
Default: `3`  

### `backoff`

Control time intervals between retry attempts.


Type: `object`  

### `backoff.initial_interval`

The initial period to wait between retry attempts.


Type: `string`  
Default: `"1s"`  

### `backoff.max_interval`

The maximum period to wait between retry attempts.


Type: `string`  
Default: `"5s"`  

### `backoff.max_elapsed_time`

The maximum period to wait before retry attempts are abandoned. If zero then no limit is used.


Type: `string`  
Default: `"30s"`  

### `max_in_flight`

The maximum number of messages to have in flight at a given time. Increase this to improve throughput.


Type: `number`  
Default: `1`  

### `batching`

Allows you to configure a [batching policy](/docs/configuration/batching).


Type: `object`  

```yaml
# Examples

batching:
  byte_size: 5000
  count: 0
  period: 1s

batching:
  count: 10
  period: 1s

batching:
  check: this.contains("END BATCH")
  count: 0
  period: 1m
```

### `batching.count`

A number of messages at which the batch should be flushed. If `0` disables count based batching.


Type: `number`  
Default: `0`  

### `batching.byte_size`

An amount of bytes at which the batch should be flushed. If `0` disables size based batching.


Type: `number`  
Default: `0`  

### `batching.period`

A period in which an incomplete batch should be flushed regardless of its size.


Type: `string`  
Default: `""`  

```yaml
# Examples

period: 1s

period: 1m

period: 500ms
```

### `batching.check`

A [Bloblang query](/docs/guides/bloblang/about/) that should return a boolean value indicating whether a message should end a batch.


Type: `string`  
Default: `""`  

```yaml
# Examples

check: this.type == "end_of_transaction"
```

### `batching.processors`

A list of [processors](/docs/components/processors/about) to apply to a batch as it is flushed. This allows you to aggregate and archive the batch however you see fit. Please note that all resulting messages are flushed as a single batch, therefore splitting the batch into smaller batches using these processors is a no-op.


Type: `array`  
Default: `[]`  

```yaml
# Examples

processors:
  - archive:
      format: lines

processors:
  - archive:
      format: json_array

processors:
  - merge_json: {}
```

