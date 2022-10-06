# APISIX monitoring
## APISIX  performance from `apisix prometheus plugin`
SkyWalking leverages OpenTelemetry Collector to transfer the metrics to
[OpenTelemetry receiver](opentelemetry-receiver.md) and into the [Meter System](./../../concepts-and-designs/meter.md).

### Data flow
1. [APISIX Prometheus plugin](https://apisix.apache.org/docs/apisix/plugins/prometheus/) collects metrics data from APSIX.
2. OpenTelemetry Collector fetches metrics from [APISIX Prometheus plugin](https://apisix.apache.org/docs/apisix/plugins/prometheus/) via Prometheus Receiver and pushes metrics to SkyWalking OAP Server via the OpenCensus gRPC Exporter or OpenTelemetry gRPC exporter.
3. The SkyWalking OAP Server parses the expression with [MAL](../../concepts-and-designs/mal.md) to filter/calculate/aggregate and store the results.

### Set up
1. Enable APISIX [APISIX Prometheus plugin](https://apisix.apache.org/docs/apisix/plugins/prometheus/) .
2. Set up [OpenTelemetry Collector ](https://opentelemetry.io/docs/collector/getting-started/#docker). For details on Prometheus Receiver in OpenTelemetry Collector, refer to [here](../../../../test/e2e-v2/cases/apisix/otel-collector/otel-collector-config.yaml).
3. Config SkyWalking [OpenTelemetry receiver](opentelemetry-receiver.md).

### MySQL Monitoring
[APISIX prometheus plugin](https://apisix.apache.org/docs/apisix/plugins/prometheus/) provide multiple dimensions metrics for APISIX server , node , route , etc. 
Accordingly, Skywalking observes the status, payload, and latency of the APISIX server, which is cataloged as a `LAYER: APISIX` `Service` in the OAP. Meanwhile, the server node would be recognized as `instance, and the route rule would be recognized as `endpoint`.

#### Supported Metrics 
| Monitoring Panel                    | Unit | Metric Name |Catalog | Description                                                                                                       | Data Source |
|-------------------------------------|------|-----|-----|-------------------------------------------------------------------------------------------------------------------|-----|
| HTTP status                         |  | meter_apisix_sv_http_status |Service | The increment rate of the status of HTTP requests                                                                 | APISIX Prometheus plugin|
| HTTP latency                        |  | meter_apisix_sv_http_latency |Service | The increment rate of the latency of HTTP requests                                                                | APISIX Prometheus plugin|
| HTTP bandwidth                      | KB | meter_apisix_sv_bandwidth |Service | The increment rate of the bandwidth of HTTP requests                                                              | APISIX Prometheus plugin|
| HTTP status of non-matched requests |  | meter_apisix_sv_http_status |Service | The increment rate of the status of HTTP requests, which don't match any route                                    | APISIX Prometheus plugin|
| HTTP latency non-matched requests   |  | meter_apisix_sv_http_latency |Service | The increment rate of the latency of HTTP requests, which don't match any route                                   | APISIX Prometheus plugin|
| HTTP bandwidth non-matched requests | KB | meter_apisix_sv_bandwidth |Service | The increment rate of the bandwidth of HTTP requests ,which don't match any route                                 | APISIX Prometheus plugin|
| HTTP connection                     |  | meter_apisix_sv_http_connections |Service | The avg number of the connections                                                                                 | APISIX Prometheus plugin|
| HTTP Request Trend                  |  | meter_apisix_http_requests |Service | The increment rate of HTTP requests                                                                               | APISIX Prometheus plugin|
| Shared dict capacity                | MB  | meter_apisix_shared_dict_capacity_bytes |Service | The avg capacity of shared dict capacity                                                                          | APISIX Prometheus plugin|
| Shared free space                   | MB  | meter_apisix_shared_dict_free_space_bytes |Service | The avg free space of shared dict capacity                                                                        | APISIX Prometheus plugin|
| etcd index                          |   | meter_apisix_sv_etcd_indexes |Service | etcd modify index for APISIX keys                                                                                 | APISIX Prometheus plugin|
| etcd latest reachability            |   | meter_apisix_sv_etcd_reachable |Service | etcd latest reachable , See [APISIX Prometheus plugin](https://apisix.apache.org/docs/apisix/plugins/prometheus/) | APISIX Prometheus plugin|
| HTTP status                         |  | meter_apisix_node_http_status |Instance | The increment rate of the status of HTTP requests                                                                 | APISIX Prometheus plugin|
| HTTP latency                        |  | meter_apisix_node_http_latency |Instance | The increment rate of the latency of HTTP requests                                                                | APISIX Prometheus plugin|
| HTTP bandwidth                      | KB | meter_apisix_node_bandwidth |Endpoint | The increment rate of the bandwidth of HTTP requests                                                              | APISIX Prometheus plugin|
| HTTP status                         |  | meter_apisix_route_http_status |Endpoint | The increment rate of the status of HTTP requests                                                                 | APISIX Prometheus plugin|
| HTTP latency                        |  | meter_apisix_route_http_latency |Endpoint | The increment rate of the latency of HTTP requests                                                                | APISIX Prometheus plugin|
| HTTP bandwidth                      | KB | meter_apisix_route_bandwidth |Endpoint | The increment rate of the bandwidth of HTTP requests                                                              | APISIX Prometheus plugin|

### Customizations
You can customize your own metrics/expression/dashboard panel.
The metrics definition and expression rules are found in `/config/otel-rules/apisix.yaml`.
The MySQL dashboard panel configurations are found in `/config/ui-initialized-templates/apisix`.