influxdb
=========

An Ansible role which installs and configures 'the I' (InfluxDB) of the TICK stack from InfluxData.

Requirements
------------

Currently this role is developed for and tested on Debian GNU/Linux (release: jessie). It is assumed to work on other Debian distributions as well.

Example
----------------

    - hosts: influxdb-servers
      roles:
         - { role: influxdb }


Role Variables
--------------

Available variables are listed below, along with default values (see defaults/main.yml):

- `influxdb_config_reporting_disabled` 
> description: The reporting-disabled option toggles the reporting of data every 24 hours to usage.influxdata.com
>
> default: "true"

- `influxdb_config_hostname`
> description: If hostname (on the OS) doesn't return a name that can be resolved by the other systems in the cluster, you'll have to set the hostname to an IP or something that can be resolved here.
>
> default: "{{ ansible_fqdn }}"

- `influxdb_config_meta_dir` 
> description: The meta directory. Files in the meta directory include meta.db.
>
> default: "/var/lib/influxdb/meta"

- `influxdb_config_meta_retention_autocreate`
> description: Retention policy auto-creation automatically creates the DEFAULT retention policy autogen when a database is created.
>
> default: "true"

- `influxdb_config_meta_logging_enabled`
> description: Meta logging toggles the logging of messages from the meta service.
>
> default: "true"

- `influxdb_config_data_dir` 
> description: The directory where InfluxDB stores the data. This directory may be changed.
>
> default: "/var/lib/influxdb/data"

- `influxdb_config_data_wal_dir` 
> description: The WAL directory is the location of the write ahead log.
>
> default: "/var/lib/influxdb/wal"

- `influxdb_config_data_trace_logging_enabled`
> description: Toggles logging of additional debug information within the TSM engine and WAL.
>
> default: "false"

- `influxdb_config_data_query_log_enabled` 
> description: The query log enabled setting toggles the logging of parsed queries before execution. Very useful for troubleshooting, but will log any sensitive data contained within a query.
>
> default: "true"

- `influxdb_config_data_cache_max_memory_size`
> description: The cache maximum memory size is the maximum size (in bytes) a shard’s cache can reach before it starts rejecting writes.
>
> default: 1048576000

- `influxdb_config_data_cache_snapshot_memory_size`
> description: The cache snapshot memory size is the size at which the engine will snapshot the cache and write it to a TSM file, freeing up memory.
>
> default: 26214400

- `influxdb_config_data_cache_snapshot_write_cold_duration`
> description: The cache snapshot write cold duration is the length of time at which the engine will snapshot the cache and write it to a new TSM file if the shard hasn’t received writes or deletes.
> 
> default: "10m"

- `influxdb_config_data_compact_full_write_cold_duration`
> description: The compact full write cold duration is the duration at which the engine will compact all TSM files in a shard if it hasn’t received a write or delete.
>
> default: "4h"

- `influxdb_config_data_max_series_per_database`
> description: The maximum number of series allowed per database. The default setting is one million. Change the setting to 0 to allow an unlimited number of series per database.
> 
> default: 1000000

- `influxdb_config_data_max_values_per_tag`
> description: The maximum number of tag values allowed per tag key. The default setting is 100000. Change the setting to 0 to allow an unlimited number of tag values per tag key. If a tag value causes the number of tag values of a tag key to exceed max-values-per-tag InfluxDB will not write the point, and it returns a partial write error. Any existing tag keys with tag values that exceed max-values-per-tag will continue to accept writes, but writes that create a new tag value will fail.
> 
> default: 100000


- `influxdb_config_coordinator_write_timeout`
> description: The time within which a write request must complete on the cluster.
> default: "10s"

- `influxdb_config_coordinator_max_concurrent_queries`
> description: The maximum number of running queries allowed on your instance. The default setting (0) allows for an unlimited number of queries.
> 
> default: 0

- `influxdb_config_coordinator_query_timeout`
> description: The maximum time for which a query can run on your instance before InfluxDB kills the query. The default setting (0) allows queries to run with no time restrictions. This setting is a duration literal.
> 
> default: "0s"

- `influxdb_config_coordinator_log_queries_after`
> description: The maximum time a query can run after which InfluxDB logs the query with a Detected slow query message. The default setting ("0") will never tell InfluxDB to log the query. This setting is a duration literal.
> 
> default: "0s"

- `influxdb_config_coordinator_max_select_point`
> description: The maximum number of points that a SELECT statement can process. The default setting (0) allows the SELECT statement to process an unlimited number of points.
> 
> default: 0

- `influxdb_config_coordinator_max_select_series`
> description: The maximum number of series that a SELECT statement can process. The default setting (0) allows the SELECT statement to process an unlimited number of series.
> 
> default: 0

- `influxdb_config_coordinator_max_select_buckets`
> description: The maximum number of GROUP BY time() buckets that a query can process. The default setting (0) allows a query to process an unlimited number of buckets.
> 
> default: 0


- `influxdb_config_retention_enabled`
> description: Set to false to prevent InfluxDB from enforcing retention policies.
> 
> default: "true"

- `influxdb_config_retention_check_interval`
> description: The rate at which InfluxDB checks to enforce a retention policy.
> 
> default: "30m"


- `influxdb_config_shard_precreation_enabled`
> description: Controls the precreation of shards so that shards are available before data arrive. Only shards that, after creation, will have both a start- and end-time in the future are ever created. Shards that would be wholly or partially in the past are never precreated.
> 
> default: "true"

- `influxdb_config_shard_precreation_check_interval`
> description: TBD
> 
> default: "10m"

- `influxdb_config_shard_precreation_advance_period`
> description: The maximum period in the future for which InfluxDB precreates shards. The 30m default should work for most systems. Increasing this setting too far in the future can cause inefficiencies.
> 
> default: "30m"

- `influxdb_config_monitor_store_enabled`
> description: Set to false to disable recording statistics internally. If set to false it will make it substantially more difficult to diagnose issues with your installation.
> 
> default: "true"

- `influxdb_config_monitor_store_database`
> description: The destination database for recorded statistics.
> 
> default: "_internal"

- `influxdb_config_monitor_store_interval`
> description: The interval at which InfluxDB records statistics.
> 
> default: "10s"

- `influxdb_config_admin_enabled`
> description: Set to true to enable the admin interface.
> 
> default: "false"

- `influxdb_config_admin_bind_address`
> description: The port used by the admin interface.
> 
> default: ":8083"

- `influxdb_config_admin_https_enabled`
> description: Set to true to enable HTTPS for the admin interface.
> 
> default: "false"

- `influxdb_config_admin_https_certificate`
> description: The path of the certificate file.
> 
> default: "/etc/ssl/influxdb.pem"


- `influxdb_config_http_enabled`
> description: Set to false to disable HTTP. Note that the InfluxDB command line interface (CLI) connects to the database using the HTTP API.
> 
> default: "true"

- `influxdb_config_http_bind_address`
> description: The port used by the HTTP API.
> 
> default: ":8086"

- `influxdb_config_http_auth_enabled`
> description: Set to true to require authentication.
> 
> default: "false"

- `influxdb_config_http_realm`
> description: Realm is the JWT realm used by the http endpoint.
> 
> default: "InfluxDB"

- `influxdb_config_http_log_enabled`
> description: Set to false to disable logging.
> 
> default: "true"

- `influxdb_config_http_write_tracing`
> description: Set to true to enable logging for the write payload. If set to true, this will duplicate every write statement in the logs and is thus not recommended for general use.
> 
> default: "false"

- `influxdb_config_http_pprof_enabled`
> description: Determines whether the pprof endpoint is enabled. This endpoint is used for troubleshooting and monitoring.
> 
> default: "true"

- `influxdb_config_http_https_enabled`
> description: Set to true to enable HTTPS.
> 
> default: "false"

- `influxdb_config_http_https_certificate`
> description: The path of the certificate file.
> 
> default: "/etc/ssl/influxdb.pem"

- `influxdb_config_http_https_private_key`
> description: The separate private key location. If only the https-certificate is specified, the httpd service will try to load the private key from the https-certificate file. If a separate https-private-key file is specified, the httpd service will load the private key from the https-private-key file.
> 
> default: ""

- `influxdb_config_http_shared_sercret`
> description: The shared secret used for JWT signing.
> 
> default: ""

- `influxdb_config_http_max_row_limit`
> description: This limits the number of rows that can be returned in a non-chunked query.
> 
> default: 10000

- `influxdb_config_http_max_connection_limit`
> description: Limit the number of connections for the http service. 0 is unlimited.
> 
> default: 0

- `influxdb_config_http_unix_socket_enabled`
> description: Set to true to enable http service over unix domain socket.
> 
> default: "false"

- `influxdb_config_http_bind_socket`
> description: The path of the unix domain socket.
> 
> default: "/var/run/influxdb.sock"

- `influxdb_config_subscriber_enabled`
> description: Set to false to disable the subscriber service.
> 
> default: "true"

- `influxdb_config_subscriber_http_timeout`
> description: Controls how long an http request for the subscriber service will run before it times out.
> 
> default: "30s"

- `influxdb_config_subscriber_insecure_skip_verify`
> description: Allows insecure HTTPS connections to subscribers. This is useful when testing with self-signed certificates.
> 
> default: "false"

- `influxdb_config_subscriber_ca_certs`
> description: The path to the PEM encoded CA certs file. If the empty string, the default system certs will be used.
> 
> default: ""

- `influxdb_config_subscriber_write_concurrency`
> description: The number of writer goroutines processing the write channel.
> 
> default: 40

- `influxdb_config_subscriber_write_buffer_size`
> description: The number of in-flight writes buffered in the write channel.
> 
> default: 1000


- `influxdb_config_graphite_enabled`
> description: Set to true to enable Graphite input.
> 
> default: "false"

- `influxdb_config_graphite_database`
> description: The name of the database that you want to write to.
> 
> default: "graphite"

- `influxdb_config_graphite_retention_policy`
> description: The relevant retention policy. An empty string is equivalent to the database’s DEFAULT retention policy.
> 
> default: ""

- `influxdb_config_graphite_bind_address`
> description: The default bind-address:port.
> 
> default: ":2003"

- `influxdb_config_graphite_protocol`
> description: Set to tcp or udp.
> 
> default: "tcp"

- `influxdb_config_graphite_consistency_level`
> description: The number of nodes that must confirm the write. If the requirement is not met the return value will be either partial write if some points in the batch fail or write failure if all points in the batch fail. For more information, see the Query String Parameters for Writes section in the Line Protocol Syntax Reference.
> 
> default: "one"

- `influxdb_config_graphite_batch_size`
> description: The input will flush if this many points get buffered.
> 
> default: 5000

- `influxdb_config_graphite_batch_pending`
> description: The number of batches that may be pending in memory.
> 
> default: 10

- `influxdb_config_graphite_batch_timeout`
> description: The input will flush at least this often even if it hasn’t reached the configured batch-size.
> 
> default: "1s"

- `influxdb_config_graphite_udp_read_buffer`
> description: UDP Read buffer size, 0 means OS default. UDP listener will fail if set above OS max.
> 
> default: 0

- `influxdb_config_graphite_separator`
> description: This string joins multiple matching ‘measurement’ values providing more control over the final measurement name.
> 
> default: "."

- `influxdb_config_graphite_tags`
> description: TBD
> 
> default: "['region=us-east', 'zone=1c']"

- `influxdb_config_graphite_templates`
> description: TBD
> 
> default: "[]"

- `influxdb_config_collectd_enabled`
> description: Set to true to enable collectd writes.
> 
> default: "false"

- `influxdb_config_collectd_bind_address`
> description: The address:port.
> 
> default: ":25826"

- `influxdb_config_collectd_database`
> description: The name of the database that you want to write to. This defaults to collectd.
> 
> default: "collectd"

- `influxdb_config_collectd_retention_policy`
> description: The relevant retention policy. An empty string is equivalent to the database’s DEFAULT retention policy.
> 
> default: ""

- `influxdb_config_collectd_typesdb`
> description: Defaults to /usr/share/collectd/types.db. A sample types.db file can be found here.
> 
> default: "/usr/share/collectd/types.db"

- `influxdb_config_collectd_security_level`
> description: TBD
> 
> default: "none"

- `influxdb_config_collectd_auth_file`
> description: TBD
> 
> default: "/etc/collectd/auth_file"

- `influxdb_config_collectd_batch_size`
> description: The input will flush if this many points get buffered.
> 
> default: 5000

- `influxdb_config_collectd_batch_pending`
> description: The number of batches that may be pending in memory.
> 
> default: 10

- `influxdb_config_collectd_batch_timeout`
> description: The input will flush at least this often even if it hasn’t reached the configured batch-size.
> 
> default: "10s"

- `influxdb_config_collectd_read_buffer`
> description: UDP Read buffer size, 0 means OS default. UDP listener will fail if set above OS max.
> 
> default: 0

- `influxdb_config_opentsdb_enabled`
> description: Set to true to enable openTSDB writes.
>
> default: "false"

- `influxdb_config_opentsdb_bind_address`
> description: The default address:port.
> 
> default: ":4242"

- `influxdb_config_opentsdb_database`
> description: The name of the database that you want to write to. If the database does not exist, it will be created automatically when the input is initialized.
> 
> default: "opentsdb"

- `influxdb_config_opentsdb_retention_policy` 
> description: The relevant retention policy. An empty string is equivalent to the database’s DEFAULT retention policy.
> 
> default: ""

- `influxdb_config_opentsdb_consistency_level`
> description: Sets the write consistency level: any, one, quorum, or all for writes.
> 
> default: "one"

- `influxdb_config_opentsdb_tls_enabled`
> description: TBD
> 
> default: "false"

- `influxdb_config_opentsdb_certificate`
> description: TBD
> 
> default: "/etc/ssl/influxdb.pem"

- `influxdb_config_opentsdb_log_point_errors`
> description: Log an error for every malformed point.
> 
> default: "true"

- `influxdb_config_opentsdb_batch_size`
> description: The input will flush if this many points get buffered.
> 
> default: 1000

- `influxdb_config_opentsdb_batch_pending`
> description: The number of batches that may be pending in memory.
> 
> default: 5

- `influxdb_config_opentsdb_batch_timeout`
> description: The input will flush at least this often even if it hasn’t reached the configured batch-size.
> 
> default: "1s"

- `influxdb_config_udp_enabled`
> description: Set to true to enable writes over UDP.
> 
> default: "false"

- `influxdb_config_udp_bind_address`
> description: An empty string is equivalent to 0.0.0.0.
> 
> default: ":8089"

- `influxdb_config_udp_database`
> description: The name of the database that you want to write to.
> 
> default: "udp"

- `influxdb_config_udp_retention_policy`
> description: The relevant retention policy for your data. An empty string is equivalent to the database’s DEFAULT retention policy.
> 
> default: ""

- `influxdb_config_udp_batch_size`
> description: The input will flush if this many points get buffered.
> 
> default: 5000

- `influxdb_config_udp_batch_pending`
> description: The number of batches that may be pending in memory.
> 
> default: 10

- `influxdb_config_udp_batch_timeout`
> description: The input will flush at least this often even if it hasn’t reached the configured batch-size.
> 
> default: "1s"

- `influxdb_config_udp_read_buffer`
> description: UDP read buffer size, 0 means OS default. UDP listener will fail if set above OS max.
> 
> default: 0

- `influxdb_config_continuous_queries_enabled`
> description: Set to false to disable CQs.
> 
> default: "true"

- `influxdb_config_continuous_queries_log_enabled`
> description: Set to false to disable logging for CQ events.
> 
> default: "true"

- `influxdb_config_continuous_queries_run_interval`
> description: The interval at which InfluxDB checks to see if a CQ needs to run. Set this option to the lowest interval at which your CQs run. For example, if your most frequent CQ runs every minute, set run-interval to 1m.
> 
> default: "1s"


Dependencies
------------

None.

License
-------

MIT

Author Information
------------------

* Patrick Ringl
