# ymlPrometheus

## [Setting it up](https://prometheus.io/docs/prometheus/latest/getting_started/)

1. Download and install with wget

   1. Wget [Download link](https://prometheus.io/download/)

2. ```bash
   tar xvfz prometheus-*.tar.gz
   cd prometheus-*
   ```

3. Configure config file in prometheus.yml

   ```yaml
   global:
     scrape_interval:     15s # By default, scrape targets every 15 seconds.
   
     # Attach these labels to any time series or alerts when communicating with
     # external systems (federation, remote storage, Alertmanager).
     external_labels:
       monitor: 'codelab-monitor'
   
   # A scrape configuration containing exactly one endpoint to scrape:
   # Here it's Prometheus itself.
   scrape_configs:
     # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
     - job_name: 'prometheus'
   
       # Override the global default and scrape targets from this job every 5 seconds.
       scrape_interval: 5s
   
       static_configs:
         - targets: ['localhost:9090']
   ```

`For config options `

[Config Options](https://prometheus.io/docs/prometheus/latest/configuration/configuration/)

4. Start prometheus with command

   ```yaml
    # Wait for a couple of minutes for prometheus to collect data!
   ./prometheus --config.file=prometheus.yml
   ```

5. View data from the webpage.

   ```b
   Instance IP:8080
   ```

   

# Collecting Data from Clients

## Starting up some sample targets

The Go client library includes an example which exports fictional RPC latencies for three services with different latency distributions.

Ensure you have the [Go compiler installed](https://golang.org/doc/install) and have a [working Go build environment](https://golang.org/doc/code.html) (with correct `GOPATH`) set up.

Download the Go client library for Prometheus and run three of these example processes:

```
# Fetch the client library code and compile example.
git clone https://github.com/prometheus/client_golang.git
cd client_golang/examples/random
go get -d
go build

# Start 3 example targets in separate terminals:
./random -listen-address=:8080
./random -listen-address=:8081
./random -listen-address=:8082
```

You should now have example targets listening on http://localhost:8080/metrics, http://localhost:8081/metrics, and http://localhost:8082/metrics.

#### [Configuring Prometheus to monitor the sample targets](https://prometheus.io/docs/prometheus/latest/getting_started/#downloading-and-running-prometheus)

- Configure Prometheus to scrape these new targets. 

- Group all three endpoints into one job called `example-random`. 
- Imagine that the first two endpoints are production targets, 
- The third one represents a canary instance. 
- To model this in Prometheus, 
  -  Add 2 groups of endpoints to a single job, 
  - Adding extra labels to each group of targets.
  - Add the `group="production"` label to the production group of targets
  - Add `group="canary"` to the canary group of targets

- Add the job definition to the `scrape_configs` section in your `prometheus.yml` and restart Prometheus instance:

```
scrape_configs:
  - job_name:       'example-random'

    # Override the global default and scrape targets from this job every 5 seconds.
    scrape_interval: 5s

    static_configs:
      - targets: ['localhost:8080', 'localhost:8081']
        labels:
          group: 'production'

      - targets: ['localhost:8082']
        labels:
          group: 'canary'
```

Go to the expression browser and verify that Prometheus now has information about time series that these example endpoints expose, such as the `rpc_durations_seconds` metric.

#### [Configure rules for aggregating scraped data into new time series](https://prometheus.io/docs/prometheus/latest/getting_started/#configure-rules-for-aggregating-scraped-data-into-new-time-series)

Queries that aggregate over thousands of time series can get slow when computed ad-hoc.

To make this more efficient, Prometheus allows prerecording of expressions into completely new persisted time series via configured recording rules. 

For example,

 RPCs (`rpc_durations_seconds_count`) averaged over all instances (but preserving the `job` and `service` dimensions) as measured over a window of 5 minutes. We could write this as:

```
avg(rate(rpc_durations_seconds_count[5m])) by (job, service)
```

Try graphing this expression.

To record the time series resulting from this expression into a new metric called `job_service:rpc_durations_seconds_count:avg_rate5m`, create a file with the following recording rule and save it as `prometheus.rules.yml`:

```
groups:
- name: example
  rules:
  - record: job_service:rpc_durations_seconds_count:avg_rate5m
    expr: avg(rate(rpc_durations_seconds_count[5m])) by (job, service)
```

To make Prometheus pick up this new rule, add a `rule_files` statement in your `prometheus.yml`. The config should now look like this:

```
global:
  scrape_interval:     15s # By default, scrape targets every 15 seconds.
  evaluation_interval: 15s # Evaluate rules every 15 seconds.

  # Attach these extra labels to all timeseries collected by this Prometheus instance.
  external_labels:
    monitor: 'codelab-monitor'

rule_files:
  - 'prometheus.rules.yml'

scrape_configs:
  - job_name: 'prometheus'

    # Override the global default and scrape targets from this job every 5 seconds.
    scrape_interval: 5s

    static_configs:
      - targets: ['localhost:9090']

  - job_name:       'example-random'

    # Override the global default and scrape targets from this job every 5 seconds.
    scrape_interval: 5s

    static_configs:
      - targets: ['localhost:8080', 'localhost:8081']
        labels:
          group: 'production'

      - targets: ['localhost:8082']
        labels:
          group: 'canary'
```

Restart Prometheus with the new configuration and verify that a new time series with the metric name `job_service:rpc_durations_seconds_count:avg_rate5m` is now available by querying it through the expression browser or graphing it.