---
title: multilevel
type: cache
status: stable
---

<!--
     THIS FILE IS AUTOGENERATED!

     To make changes please edit the corresponding source file under internal/impl/<provider>.
-->

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

Combines multiple caches as levels, performing read-through and write-through operations across them.

```yml
# Config fields, showing default values
label: ""
multilevel: [] # No default (required)
```

## Examples

<Tabs defaultValue="Hot and cold cache" values={[
{ label: 'Hot and cold cache', value: 'Hot and cold cache', },
]}>

<TabItem value="Hot and cold cache">

The multilevel cache is useful for reducing traffic against a remote cache by routing it through a local cache. In the following example requests will only go through to the memcached server if the local memory cache is missing the key.

```yaml
pipeline:
  processors:
    - branch:
        processors:
          - cache:
              resource: leveled
              operator: get
              key: ${! json("key") }
          - catch:
            - mapping: 'root = {"err":error()}'
        result_map: 'root.result = this'

cache_resources:
  - label: leveled
    multilevel: [ hot, cold ]

  - label: hot
    memory:
      default_ttl: 60s

  - label: cold
    memcached:
      addresses: [ TODO:11211 ]
      default_ttl: 60s
```

</TabItem>
</Tabs>


