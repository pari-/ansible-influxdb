# influxdb

An Ansible role which installs and configures 'the I' (InfluxDB) of the TICK stack from InfluxData.

## Requirements

Currently this role is developed for and tested on Debian GNU/Linux (release: jessie). It is assumed to work on other Debian distributions as well.

Ansible version in use for development: 2.2.0

## Example

```yaml
- hosts: influxdb-servers
  roles:
     - { role: influxdb }
```

## Enabling a service

One of the following is a `service` and can be used multiple times within InfluxDBs configuration namespace:
- collectd
- graphite
- optentsdb
- udp

By default there's no service enabled, though. To enable a service, do the following:

1. Adapt the graphite service defaults within the [graphite section] of the configuration YAML dict:

```yaml
influxdb_service_defaults:
  graphite:
    enabled: true
    database: "graphite"
    retention-policy: ""
    bind-address: ":2003"
    protocol: "tcp"
    consistency-level: "one"
    batch-size: 5000
    batch-pending: 10
    batch-timeout: "1s"
    udp-read-buffer: 0
    separator: "."
  ...
```

2. After that, simply add the service to the `ìnfluxdb_enabled_services`-YAML dict:

```yaml
ìnfluxdb_enabled_services:
  - section: graphite
  ...
```

## Defining multiple services of the same kind

Simply add multiple services (including configuration overrides) like this:

```yaml
ìnfluxdb_enabled_services:
  - section: graphite
    bind-address: ":2003"
  ...
  - section: graphite
    bind-address: ":2004"
  ...
```



## Role Variables

Available variables are listed below, along with default values (see defaults/main.yml):

### influxdb_config
Global options:

```yaml
influxdb_config:
  reporting-disabled: false
  hostname: "{{ ansible_hostname }}"
```
Upstream documentation: [config/global-options][0]

- `reporting-disabled`

  > Change this option to true to disable reporting.
  >
  > `false`

- `hostname`

  > InfluxDB tries  to get the hostname automatically, but if it the os returns something that isn't resolvable by other servers in the cluster, this option manually sets the hostname
  >
  > `"{{ ansible_hostname }}"`

#### [meta section]
This section controls parameters for InfluxDB’s metastore, which stores information on users, databases, retention policies, shards, and continuous queries.

```yaml
influxdb_config:
  meta:
    dir: "/var/lib/influxdb/meta"
    retention-autocreate: true
    logging-enabled: true
```
Upstream documentation: [config/meta][1]

- `dir`

  > Where the metadata/raft database is stored.
  >
  > `"/var/lib/influxdb/meta"`

- `retention-autocreate`

  > Automatically create a default retention policy when creating a database. 
  >
  > `true`

- `logging-enabled`

  > If log messages are printed for the meta service
  >
  > `true`

#### [data section] 
This section controls where the actual shard data for InfluxDB lives and how it is flushed from the WAL. dir may need to be changed to a suitable place for you system, but the WAL settings are an advanced configuration. The defaults should work for most systems.

```yaml
influxdb_config:
  data:
    dir: "/var/lib/influxdb/data"
    wal-dir: "/var/lib/influxdb/wal"
    trace-logging-enabled: false
    query-log-enabled: true
    cache-max-memory-size: 1073741824
    cache-snapshot-memory-size: 26214400
    cache-snapshot-write-cold-duration: "10m0s"
    compact-full-write-cold-duration: "4h0m0s"
    max-series-per-database: 1000000
    max-values-per-tag: 100000
```
Upsteam documentation: [config/data][2]

- `dir`

  > The directory where the TSM storage engine stores TSM files.
  >
  > `"/var/lib/influxdb/data"`

- `wal-dir`

  > The directory where the TSM storage engine stores WAL files. 
  >
  > `"/var/lib/influxdb/wal"`

- `trace-logging-enabled`

  > Trace logging provides more verbose output around the tsm engine. Turning this on can provide more useful output for debugging tsm engine issues.
  >
  > `false`

- `query-log-enabled`

  > Whether queries should be logged before execution. Very useful for troubleshooting, but will log any sensitive data contained within a query. 
  >
  > `true`

- `cache-max-memory-size`

  > The maximum size a shard's cache can reach before it starts rejecting writes.
  >
  > `1073741824`

- `cache-snapshot-memory-size`

  > The size at which the engine will snapshot the cache and write it to a TSM file, freeing up memory
  >
  > `26214400`

- `cache-snapshot-write-cold-duration`

  > The length of time at which the engine will snapshot the cache and write it to a new TSM file if the shard hasn't received writes or deletes
  >
  > `"10m0s"`

- `compact-full-write-cold-duration`

  > The duration at which the engine will compact all TSM files in a shard if it hasn't received a write or delete
  >
  > `"4h0m0s"`

- `max-series-per-database`

  > The maximum series allowed per database before writes are dropped.  This limit can prevent high cardinality issues at the database level.  This limit can be disabled by setting it to 0.
  >
  > `1000000`

- `max-values-per-tag`

  > The maximum number of tag values per tag that are allowed before writes are dropped. This limit can prevent high cardinality tag values from being written to a measurement.  This limit can be disabled by setting it to 0.
  >
  > `100000`

#### [coordinator section] 
This section contains configuration options for query management.

```yaml
influxdb_config:
  coordinator:
    write-timeout: "10s"
    max-concurrent-queries: 0
    query-timeout: "0s"
    log-queries-after: "0s"
    max-select-point: 0
    max-select-series: 0
    max-select-buckets: 0
```
Upsteam documentation: [config/coordinator][3]

- `write-timeout`

  > The default time a write request will wait until a "timeout" error is returned to the caller. 
  >
  > `"10s"`

- `max-concurrent-queries`

  > The maximum number of concurrent queries allowed to be executing at one time. If a query is executed and exceeds this limit, an error is returned to the caller.  This limit can be disabled by setting it to 0.
  >
  > `0`

- `query-timeout`

  > The maximum time a query will is allowed to execute before being killed by the system. This limit can help prevent run away queries.  Setting the value to 0 disables the limit.
  >
  > `"0s"`

- `log-queries-after`

  > The the time threshold when a query will be logged as a slow query. This limit can be set to help discover slow or resource intensive queries.  Setting the value to 0 disables the slow query logging.  
  >
  > `"0s"`

- `max-select-point`

  > The maximum number of points a SELECT can process. A value of 0 will make the maximum point count unlimited.
  >
  > `0`

- `max-select-series`

  > The maximum number of series a SELECT can run. A value of zero will make the maximum series count unlimited.
  >
  > `0`

- `max-select-buckets`

  > The maxium number of group by time bucket a SELECt can create.  A value of zero will max the maximum number of buckets unlimited.
  >
  > `0`

#### [retention section]
This section controls the enforcement of retention policies for evicting old data.

```yaml
influxdb_config:
  retention:
    enabled: true
    check-interval: "30m0s"
```
Upsteam documentation: [config/retention][4]

- `enabled`

  > Determines whether retention policy enforcment enabled.
  >
  > `true`

- `check-interval`

  > The interval of time when retention policy enforcement checks run.
  >
  > `"30m0s"`

#### [shard-precreation section]
Controls the precreation of shards so that shards are available before data arrive. Only shards that, after creation, will have both a start- and end-time in the future are ever created. Shards that would be wholly or partially in the past are never precreated.

```yaml
influxdb_config:
  shard-precreation:
    enabled: true
    check-interval: "10m0s"
    advance-period: "30m0s"
```
Upsteam documentation: [config/shard-precreation][5]

- `enabled`

  > Determines whether shard pre-creation service is enabled.
  >
  > `true`

- `check-interval`

  > The interval of time when the check to pre-create new shards runs.
  >
  > `"10m0s"`

- `advance-period`

  > The default period ahead of the endtime of a shard group that its successor group is created.
  >
  > `"30m0s"`


#### [http section] 
Controls the availability of the built-in, web-based admin interface.

```yaml
influxdb_config:
  http:
    enabled: false
    bind-address: ":8086"
    auth-enabled: false
    realm: "InfluxDB"
    log-enabled: true
    write-tracing: false
    pprof-enabled: true
    https-enabled: false
    https-certificate: "/etc/ssl/influxdb.pem"
    https-private-key: ""
    shared-sercret: ""
    max-row-limit: 10000
    max-connection-limit: 0
    unix-socket-enabled: false
    bind-socket: "/var/run/influxdb.sock"
```
Upsteam documentation: [config/http][6]

- `enabled`

  > Set to false to disable HTTP. Note that the InfluxDB command line interface (CLI) connects to the database using the HTTP API.
  >
  > `false`

- `bind-address`

  > The default bind-address of the http(s) connector.
  >
  > `":8086"`

- `auth-enabled`

  > Set to true to require authentication.
  >
  > `false`

- `realm`

  > Realm is the JWT realm used by the http endpoint.
  >
  > `"InfluxDB"`

- `log-enabled`

  > Set to false to disable logging. 
  >
  > `true`

- `write-tracing`

  > Set to true to enable logging for the write payload. If set to true, this will duplicate every write statement in the logs and is thus not recommended for general use.
  >
  > `false`

- `pprof-enabled`

  > Determines whether the pprof endpoint is enabled. This endpoint is used for troubleshooting and monitoring.
  >
  > `true`

- `https-enabled`

  > Set to true to enable HTTPS.
  >
  > `false`

- `https-certificate`

  > The path of the certificate file.
  >
  > `"/etc/ssl/influxdb.pem"`

- `https-private-key`

  > The separate private key location. If only the https-certificate is specified, the httpd service will try to load the private key from the https-certificate file. If a separate https-private-key file is specified, the httpd service will load the private key from the https-private-key file. 
  >
  > `""`

- `shared-sercret`

  > The shared secret used for JWT signing.
  >
  > `""`

- `max-row-limit`

  > This limits the number of rows that can be returned in a non-chunked query.
  >
  > `10000`

- `max-connection-limit`

  > Limit the number of connections for the http service. 0 is unlimited.
  >
  > `0`

- `unix-socket-enabled`

  > Set to true to enable http service over unix domain socket.
  >
  > `false`

- `bind-socket`

  > The path of the unix domain socket.
  >
  > `"/var/run/influxdb.sock"`


#### admin section 
Controls the availability of the built-in, web-based admin interface.

```yaml
influxdb_config:
  admin:
    enabled: false
    bind-address: ":8083"
    https-enabled: false
    https-certificate: "/etc/ssl/influxdb.pem"
```
Upsteam documentation: [config/admin][7]

- `enabled`

  > Determines whether the admin service is enabled.
  >
  > `false`

- `bind-address`

  > The default bind-address of the admin connector.
  >
  > `":8083"`

- `https-enabled`

  > Whether the admin service should use HTTPS.
  >
  > `false`

- `https-certificate`

  > The SSL certificate used when HTTPS is enabled.
  >
  > `"/etc/ssl/influxdb.pem"`

#### monitor section 
This section controls InfluxDB’s system self-monitoring.

```yaml
influxdb_config:
  monitor:
    store-enabled: true
    store-database: "_internal"
    store-interval: "10s"
```
Upsteam documentation: [config/monitor][8]

- `store-enabled`

  > Whether to record statistics internally.
  >
  > `true`

- `store-database`

  > The destination database for recorded statistics
  >
  > `"_internal"`

- `store-interval`

  > The interval at which to record statistics 
  >
  > `"10s"`

#### subscriber section 
This section controls how Kapacitor will receive data.

```yaml
influxdb_config:
  subscriber:
    enabled: true
    http-timeout: "30s"
    insecure-skip-verify: false
    ca-certs: ""
    write-concurrency: 40
    write-buffer-size: 1000
```
Upsteam documentation: [config/subscriber][9]

- `enabled`

  > Determines whether the subscriber service is enabled.
  >
  > `true`

- `http-timeout`

  > The default timeout for HTTP writes to subscribers.
  >
  > `"30s"`

- `insecure-skip-verify`

  > Allows insecure HTTPS connections to subscribers. This is useful when testing with self-signed certificates.
  >
  > `false`

- `ca-certs`

  > The path to the PEM encoded CA certs file. If the empty string, the default system certs will be used
  >
  > `""`

- `write-concurrency`

  > The number of writer goroutines processing the write channel.
  >
  > `40`

- `write-buffer-size`

  > The number of in-flight writes buffered in the write channel.
  >
  > `1000`

#### continuous_queries section 
This section controls how continuous queries (CQs) run within InfluxDB. 

```yaml
influxdb_config:
  continuous_queries:
    enabled: true
    log-enabled: true
    run-interval: "1s"
```
Upsteam documentation: [config/continuous-queries][10]

- `enabled`

  > Determiens whether the continuous query service is enabled.
  >
  > `true`

- `log-enabled`

  > Controls whether queries are logged when executed by the CQ service.
  >
  > `true`

- `run-interval`

  > Interval for how often continuous queries will be checked if they need to run.
  >
  > `"1s"`

### influxdb\_service\_defaults

#### [collectd section]
The collectd input allows InfluxDB to accept data transmitted in collectd native format. This data is transmitted over UDP.

```yaml
influxdb_service_defaults:
  collectd:
    enabled: false
    bind-address: ":25826"
    database: "collectd"
    retention-policy: ""
    typesdb: "/usr/share/collectd/types.db"
    security-level: "none"
    auth-file: "/etc/collectd/auth_file"
    batch-size: 5000
    batch-pending: 10
    batch-timeout: "10s"
    read-buffer: 0
```
Upstream documentation: [config/collectd][11]

- `enabled`

  > Set to true to enable collectd writes.
  >
  > `false`

- `bind-address`

  > The default bind-address of the collectd connector.
  >
  > `":25826"`

- `database`

  > The name of the database that you want to write to.
  >
  > `"collectd"`

- `retention-policy`

  > The relevant retention policy. An empty string is equivalent to the database’s DEFAULT retention policy.
  >
  > `""`

- `typesdb`

  > The default location of the collectd types db file.
  >
  > `"/usr/share/collectd/types.db"`

- `security-level`

  > The default collectd security level.
  >
  > `"none"`

- `auth-file`

  > The default location of the collectd user/password file.
  >
  > `"/etc/collectd/auth_file"`

- `batch-size`

  > The input will flush if this many points get buffered.
  >
  > `5000`

- `batch-pending`

  > The number of batches that may be pending in memory.
  >
  > `10`

- `batch-timeout`

  > The input will flush at least this often even if it hasn’t reached the configured batch-size.
  >
  > `"10s"`

- `read-buffer`

  > UDP Read buffer size, 0 means OS default. UDP listener will fail if set above OS max.
  >
  > `0`

#### [graphite section]
This section controls one or many listeners for Graphite data.

```yaml
influxdb_service_defaults:
  graphite:
    enabled: false
    database: "graphite"
    retention-policy: ""
    bind-address: ":2003"
    protocol: "tcp"
    consistency-level: "one"
    batch-size: 5000
    batch-pending: 10
    batch-timeout: "1s"
    udp-read-buffer: 0
    separator: "."
```
Upstream documentation: [config/graphite][12]

- `enabled`

  > Set to true to enable Graphite input.
  >
  > `false`

- `database`

  > The name of the database that you want to write to.
  >
  > `"graphite"`

- `retention-policy`

  > The relevant retention policy. An empty string is equivalent to the database’s DEFAULT retention policy.
  >
  > `""`

- `bind-address`

  > The default bind-address of the graphite connector.
  >
  > `":2003"`

- `protocol`

  > Set to tcp or udp.
  >
  > `"tcp"`

- `consistency-level`

  > The number of nodes that must confirm the write. If the requirement is not met the return value will be either partial write if some points in the batch fail or write failure if all points in the batch fail.
  >
  > `"one"`

- `batch-size`

  > The input will flush if this many points get buffered.
  >
  > `5000`

- `batch-pending`

  > The number of batches that may be pending in memory.
  >
  > `10`

- `batch-timeout`

  > The input will flush at least this often even if it hasn’t reached the configured batch-size.
  >
  > `"1s"`

- `udp-read-buffer`

  > UDP Read buffer size, 0 means OS default. UDP listener will fail if set above OS max.
  >
  > `0`

- `separator`

  > This string joins multiple matching ‘measurement’ values providing more control over the final measurement name.
  >
  > `"."`


#### [opentsdb section]
Controls one or many listeners for collectd data.

```yaml
influxdb_service_defaults:
  opentsdb:
    enabled: false
    bind-address: ":4242"
    database: "opentsdb"
    retention-policy: ""
    consistency-level: "one"
    tls-enabled: false
    certificate: "/etc/ssl/influxdb.pem"
    log-point-errors: true
    batch-size: 1000
    batch-pending: 5
    batch-timeout: "1s"
```
Upstream documentation: [config/opentsdb][13]

- `enabled`

  > Set to true to enable openTSDB writes.
  >
  > `false`

- `bind-address`

  > The default bind-address of the opentsdb connector.
  >
  > `":4242"`

- `database`

  > The name of the database that you want to write to. If the database does not exist, it will be created automatically when the input is initialized.
  >
  > `"opentsdb"`

- `retention-policy`

  > The relevant retention policy. An empty string is equivalent to the database’s DEFAULT retention policy.
  >
  > `""`

- `consistency-level`

  > Sets the write consistency level: any, one, quorum, or all for writes.
  >
  > `"one"`

- `tls-enabled`

  > Enable TLS.
  >
  > `false`

- `certificate`

  > Path to SSL certificate.
  >
  > `"/etc/ssl/influxdb.pem"`

- `log-point-errors`

  > Log an error for every malformed point.
  >
  > `true`

- `batch-size`

  > The input will flush if this many points get buffered.
  >
  > `1000`

- `batch-pending`

  > The number of batches that may be pending in memory.
  >
  > `5`

- `batch-timeout`

  > The input will flush at least this often even if it hasn’t reached the configured batch-size. 
  >
  > `"1s"`

#### [udp section]
This section controls the listeners for InfluxDB line protocol data via UDP

```yaml
influxdb_service_defaults:
  udp:
    enabled: false
    bind-address: ":8089"
    database: "udp"
    retention-policy: ""
    batch-size: 5000
    batch-pending: 10
    batch-timeout: "1s"
    read-buffer: 0
    precision: ""
```
Upstream documentation: [config/udp][14]

- `enabled`

  > Set to true to enable writes over UDP.
  >
  > `false`

- `bind-address`

  > The default bind-address of the udp connector.
  >
  > `":8089"`

- `database`

  > The name of the database that you want to write to. 
  >
  > `"udp"`

- `retention-policy`

  > The relevant retention policy for your data. An empty string is equivalent to the database’s DEFAULT retention policy.
  >
  > `""`

- `batch-size`

  > The input will flush if this many points get buffered. 
  >
  > `5000`

- `batch-pending`

  > The number of batches that may be pending in memory.
  >
  > `10`

- `batch-timeout`

  > The input will flush at least this often even if it hasn’t reached the configured batch-size.
  >
  > `"1s"`

- `read-buffer`

  > UDP read buffer size, 0 means OS default. UDP listener will fail if set above OS max.
  >
  > `0`

- `precision`

  > Time precision used when decoding time values. Defaults to nanoseconds which is the default of the database.
  >
  > `""`

### Role Internals

## Dependencies

None.


## License

MIT

## Links

* [InfluxDB configuration documentation][-1]
[-1]: https://docs.influxdata.com/influxdb/v1.1/administration/config
[0]: https://docs.influxdata.com/influxdb/v1.1/administration/config/#global-options
[1]: https://docs.influxdata.com/influxdb/v1.1/administration/config/#meta
[2]: https://docs.influxdata.com/influxdb/v1.1/administration/config/#data
[3]: https://docs.influxdata.com/influxdb/v1.1/administration/config/#coordinator
[4]: https://docs.influxdata.com/influxdb/v1.1/administration/config/#retention
[5]: https://docs.influxdata.com/influxdb/v1.1/administration/config/#shard-precreation
[6]: https://docs.influxdata.com/influxdb/v1.1/administration/config/#http
[7]: https://docs.influxdata.com/influxdb/v1.1/administration/config/#admin
[8]: https://docs.influxdata.com/influxdb/v1.1/administration/config/#monitor
[9]: https://docs.influxdata.com/influxdb/v1.1/administration/config/#subscriber
[10]: https://docs.influxdata.com/influxdb/v1.1/administration/config/#continuous-queries
[11]: https://docs.influxdata.com/influxdb/v1.1/administration/config/#collectd
[12]: https://docs.influxdata.com/influxdb/v1.1/administration/config/#graphite
[13]: https://docs.influxdata.com/influxdb/v1.1/administration/config/#opentsdb
[14]: https://docs.influxdata.com/influxdb/v1.1/administration/config/#udp

## Author Information

* Patrick Ringl
