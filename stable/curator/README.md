# Chart for Curator

Elasticsearch Curator helps you curate, or manage, your Elasticsearch indices by:
* Obtaining the full list of indices from the cluster, as the *actionable list*
* Iterating through a list of user-defined [filters][5] to progressively remove indices from the *actionable* list as needed.

This chart defaults to deleting all logstash-prefixed indices older than 14 days.

## Installation

### Install via helm

To install from local:

```
helm install --name my-release --namespace my-namespace ./curator
```

To install from local, overriding the base image and tag directly:

```
helm install --name my-release --namespace my-namespace \
  --set image.name=bobrik/curator \
  --set image.tag=5.2.0 \
  ./curator
```

To install from local, overriding with a values file:

```
helm install --name my-release --namespace my-namespace \
  --values ./staging-values.yaml \
  ./curator
```

In which the file `./staging-values.yaml` contains:

```
image:
  name: bobrik/curator
  tag: 5.2.0

# sets the cron job to execute everyday at 2am
schedule: "00 2 * * *"

description:
  Delete indices older than 7 days and prefixed with logstash-.

filters:
- filtertype: pattern
  kind: prefix
  value: "logstash-"
  exclude: False
- filtertype: age
  source: name
  timestring: '%Y.%m.%d'
  direction: older
  unit: days
  unit_count: 7
  field: null
  stats_result: null
  epoch: null
  exclude: False
```

## Configuration

The following tables lists the configurable parameters of the Curator chart and their default values.

| Parameter                | Description                                     | Default                                 |
| ------------------------ | ----------------------------------------------- | --------------------------------------- |
| `name`                   | Name of the chart                               | `curator`                               |
| `image.name`             | FQDN repository/image name                      | `bobrik/curator:5.4.0`                  |
| `image.tag`              | Image tag                                       | `latest`                                |
| `schedule`               | The cron schedule                               | `30 3 * * *`                            |
| `action`                 | Name of the action                              | `delete_indices`                        |
| `description`            | Description of the action                       | `<see values.yaml>`                     |
| `options`                | [Options][6] of the action                      | `<see values.yaml>`                     |
| `filters`                | [Filters][5] of the action                      | `<see values.yaml>`                     |
| `client`                 | [Client configuration][7]                       | `<see values.yaml>`                     |
| `logging`                | [Logging configuration][7]                      | `<see values.yaml>`                     |
| `resources`              | CPU and Memory for limits and requests          | cpu: 50m , memory: 200Mi                |


## References

* [Curator github][2]
* [Curator Reference Documentation][3]
* [Discuss Curator][4]


[2]: https://github.com/elastic/curator "curator github"
[3]: https://www.elastic.co/guide/en/elasticsearch/client/curator/current/index.html "curator reference documentation"
[4]: https://discuss.elastic.co/search?q=curator "curator discussion"
[5]: https://www.elastic.co/guide/en/elasticsearch/client/curator/5.2/filters.html "curator filter documentation"
[6]: https://www.elastic.co/guide/en/elasticsearch/client/curator/5.2/options.html "curator options documentation"
[7]: https://www.elastic.co/guide/en/elasticsearch/client/curator/5.2/configfile.html "curator config file documentation"
