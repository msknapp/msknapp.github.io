# Monitoring

## Google Cloud Observability, Logging

- Can create log based metrics, and alerting policies on those metrics.  Can put them in charts and dashboards too.
- Google resource logs automatically go to cloud logging, you don’t need to enable it.
- There are libraries, modules, SDKs, etc. that help publish logs, but it’s easier to use their ops agent, which 
  manages forwarding it automatically.  GKE has an agent ootb.
- There is logs explorer and log analytics.  The log analytics lets you use SQL to find trends, and does not cost 
  more, even to query it.
- Ops agent uses fluent bit for logs, and opentelemetry for metrics and traces.  Fluentbit can handle metrics and 
  traces, but Ops agent does not use it for that.
- **Buckets:** Two come automatically, _Required and _Default.  _Required is retained for 400 days and holds records 
  of when Google intervened and altered resources for customers.  Most resource logs go to _Default, and it is retained for 30 days.
- _Required holds all audit, security, and compliance records, from account administrators as well as google staff.
- **Retention:**  400 for _Required, 30 days for _Default, you can increase retention on either.  You cannot decrease 
  retention of _Required.  You can decrease retention of _Default, but it always bills for a minimum of 30 days
- Log buckets belong to a region.
- **Billing:** No charge for queries or _Required.  Charged for streaming in log entries, and for retention above the defaults.  
  Log analytics does not cost more.  You are charged for what goes into non-required log buckets, not for what hits the router or API.
- **Re-routing:** resource and applications logs can be re-routed to different destinations, they don’t need to go to a log bucket.
  You can re-route them - to BigQuery, GCS, pubsub, or log buckets in different projects.  Might not be able to get alerts or metrics 
  from the alternatives though.  There is - no charge for routing, just charges at the destination.  To export logs to third parties like 
  Splunk requires using PubSub.
- **Categories:** platform (gcp services), component (google software run by users), security/audit (actions by customer),
  security/access transparency (actions from google personnel), user, and multi-cloud.
- Access is controlled at the bucket level, so if you grant access to a log bucket, they can see everything in that bucket.
  Privacy may be one of the - driving factors in having separate buckets.  You might also want a separate one for billing.
- There are names for logs, which correlates to a resource.  Log entries also hold information about the source it came from,
  the timestamp, and the - payload.  The payload can be structured json, text, and proto.
- **Views:** Provides access to a subset of log data.  You can use IAM to control access to the views. 
- Projects get a log router automatically.  It is the API responsible for forwarding log entries to sinks.
  A sink configures where logs get sent to for storage.  Sinks can send to one or more of: log buckets, bigquery, GCS, and pub/sub.  
  Sinks can filter down log entries.  Log entries are sent to all sinks attached to a router, so it can show up in multiple locations.
- You cannot retroactively send log entries to a sink after changing its configuration.
- Log based metrics are handled by the router before sending to any sink, and they go straight to cloud monitoring.
- Sink filters have one inclusion rule, and any number of exclusion rules, written in logging query language.
- Google logging automatically groups errors that appear to have the same cause, by filtering on severity=error, and payloads that 
  appear to be typical stack traces.  These are error groups.  It could also look at the source location, and if the text payload is the same.
- Log Query Language:
    - Think of it like the WHERE section of a SQL query, you are specifying what fields must equal or compare to what values.
    - The expression consists of a series of statements, which are like: key comparison value.
      The statements are implicitly separated by “AND” logical - operators, but you can explicitly add the operators.
      If you use “OR”, they recommend putting parentheses around segments of the expression.  You can negate operators with “NOT”.
      Spaces around the comparison are optional.  Keys can select sub-fields of the log entry, like “resource.type”, 
      similar to json path expressions.  They can select fields of the payload of structured logs.  
      Field selection always goes on the left of the comparison.
    - Logical operators must be capitalized, “AND”, “OR”, and “NOT”.
    - String matching is case insensitive, except in regular expressions.
    - If your expression lacks a comparison, it means the log entry must contain that value somewhere in any of its fields.  
      This is called a global match, and it is inefficient, so avoid it.
    - “NOT” can be replaced with a minus sign.
    - Views cannot use “OR”
    - Fields with special characters, like “/”, must be quoted.
    - Comparisons are mostly intuitive: “=”, “<”, “>”, “<=”, “>=”, “=~”, “!~”, “:”
    - Note there is no “==” or “~” comparison.
    - Comparisons with ~ are for regular expressions.
    - “:” is the “has” operator, it means the field must contain the value.  When using this, the query cannot take advantage of indexes.
    - Values can be parenthesized sets of values, separated with logical operators.
    - Many fields are automatically indexed, including them in your query can speed it up considerably.
    - If the field selected is an array, then the statement will be evaluated for each value in that array.  
      If it is true for any value, then that statement is true for that log entry.
    - Using “OR” in queries can make it slower, and may eliminate its ability to use indexes.

## Prometheus

- Stores data as time series, which is a sequence of samples.
- Samples consist of milliseconds precision timestamps, and values that are either floating point numbers, or “native histograms”.
- A time series has a name and labels.  Labels are key/value pairs.  Both keys and values of labels are strings.
  Every unique combination of name and labels, including their values, is a distinct time series.  Adding or removing one key, 
  or one value, to a time series results in a different time - series.  Names tend towards snake_case.  
  Labels represent different dimensions of a metric, and define Prometheus’s “dimensional data model”.
- Prometheus can filter and aggregate time series based on labels, so long as the metric has the same name.
- Time series are notated like: metric_name{label1=”value1”, label2=”value2”, …} which is the same as OpenTSDB.
- The Prometheus server is oblivious to metric types, but client libraries support different types:
    - **Counter:** a monotonically increasing number, it may only reset to zero on occasion, like if a server restarts.
    - **Gauge:** a number that can arbitrarily go up or down.
    - **Histograms:** keep track of samples in buckets.  Each bucket is a cumulative sum of all values below its max threshold.
      If a metric name is <base>, the histogram produces <base>_sum, <base>_bucket, and <base>_count.  
      The buckets will have the label “le” set to a threshold.  The last bucket uses - “+Inf”. 
      `<base>_count` is the same as `<base>_bucket{le:”+Inf”}`
    - **Summary:** is similar to histogram but uses quantiles instead of thresholds.
- **Instance:** an endpoint that can be scraped for metrics.  host:port
- **Job:** a set of instances with a common purpose.
- Job and instance labels are automatically applied to metrics.
- Prometheus uses a pull model to collect metrics over HTTP, but you can push metrics using an intermediate gateway.
  The push gateway can be required - for short lived jobs.  The main server scrapes metrics from others.
- A pull based model means you can make Prometheus highly available by simply adding more nodes.  Each collects the same information, 
  they do not need to share anything between them.  If they send alerts, the alerts get de-duplicated by the alert manager.
- A pull based model means it’s easier to notice when servers are down,
- It also means you can view the metrics of any server directly from the browser.
- Services scraped are called “targets”.
- Prometheus can use the K8s API server to discover targets to scrape.
- You can define rules which derive new time series and can send alerts.  They get evaluated on an evaluation_interval in the config.
- You can configure a scrape interval, even by target.
- Targets are expected to have a “/metrics” endpoint.
- You can specify targets to scrape in its config file.  It can also discover targets.
- You can update its configuration without a reboot by sending a NOHUP signal.

### Querying

- The simplest query is a time series notation.  You don’t specify the exact time series, you specify filter conditions.
  The syntax is: `metric_name- {key=”value”, …}`, you can omit label criteria, then it returns all time series for that metric.  
  These will be graphed as separate lines per time - series, so you probably want to aggregate them somehow.  
  You can filter on the same key more than once in an expression
- You can wrap the metrics with functions like “count” or “rate”.
- Label matching operators: “=”, “=~” for regex, “!~” for not regex, “!=”.  Regex is always fully anchored.
- The default lookback period is five minutes.  If a time series does not have a value within that lookback period, it will not be returned.
- Data Types: Instant Vector, Range Vector, Scalar, and String
- **Instant Vector:** One value for each time series (unique combination of label keys/values), at the same timestamp.
- **Range Vector:** A sequence (range) of values for each time series.  I presume at equal intervals.
- To make a Range Query, take an instant query and append `[seconds]` to it.  Really you can put any float between square brackets.
  Without units, - it’s defined as seconds.  It’s common to add a unit suffix though, like “m” for minutes, “h” for hours, d for days, 
  w for weeks, y for years.  This - returns all values for each time series since that far in the past.
- After a selector, you can add “offset <seconds>” to offset the metric into the past.  Positive numbers go back in time.
  It can go after instant or - range vector selectors.
- You can specify an exact unix timestamp using the @ modifier.  You will need to convert timestamps into the unix format.
- You can do some metric aggregation.  There are aggregation functions like sum, avg, min, max, and count.
  They can optionally take a set of labels.  - The format is `aggregation [{by | without} label, …] expression` 
  The term “without” means group by every other label, or every label except what - follows.  If you aggregate “by” a set of labels,
  those will be the only labels remaining in the output vector.  If you aggregate “without” a set of labels, 
  those will be the only labels removed from the output vector.
- Some useful functions: rate, ln, log2, log10, sqrt, and exp.
- **Transfer format:** refers to the text syntax or format that must be exposed by the /metrics endpoint for scraping.
  It’s basically a list of time series identifiers, followed by a space and then their number.

## OpenTelemetry

- Replaces opencensus
- Handles these signals: metrics, traces, and logs.  Collectively these are known as telemetry.  Context is information 
  about what happened, when, and where.
- Semantic conventions are established for naming of metrics, so that data can be easily correlated and analyzed later, 
  even when it came from different places.  It’s standardized naming for seamless - analysis
- Instrumentation is the act of adding observability code to an app yourself..
- Does not store telemetry data, nor provide any way to visualize it.  It is not a timeseries database, nor a web UI.
- Enables easier instrumentation of applications with monitoring, by remaining agnostic with regards to programming language, 
  infrastructure/platform, and runtime environments.  You can use the same  framework for applications written in 
  different languages, on different infrastructure, and running on different runtime environments.
- Infrastructure or applications send telemetry to an observability backend for storage, and an observability frontend 
  queries that storage layer to enable visualization.
- Helps with generation, collection, management, and export of telemetry data.
- Mitigates against vendor lock in, because it enables you to switch your storage or visualization 
  layers without changing your application code.
- Instrumented means emitting telemetry.
- Only need to learn one single set of APIs.
- You will use an OpenTelemetry SDK to talk to the OpenTelemetry API.  When you write your code, you will be interacting 
  with the API, which is meant to have the same methods and arguments in all languages.  Code can use the API even if there 
  is no backend collecting the metrics.  If you write a library, it should only have the API.  Applications should include 
  an SDK, usually it is initialized once and has the same lifecycle as the application, the SDK is what actually exports 
  the telemetry and may depend on the services used on the backend.

## Tracing

- Enables us to see the path taken to handle or process a request, and how much time was spent in each step.
- Traces are composed of spans.  Spans have a name, start/stop times, and attributes.  They may also have events.
  Attributes are key/value pairs.  Keys tend to be period delimited and in snake_case.  
  Attribute keys must be strings, values must be a scalar or an array of scalars.  
  Traces also have a parent_id unless they are a root span.
- Spans have a duration, but events occur at a single point in time.  Events have a name, timestamp, and attributes.
- Traces have IDs and spans have IDs.  These are in the span’s context.  A context is metadata that should be passed 
  across application boundaries, like through GRPC or HTTP requests.  This is context propagation, and it enables distributed traces, 
  so a trace can show spans that occurred in different systems or applications.
- Span links: enable you to associate one span with another, even on different traces.  This lets you indicate that 
  one span was caused by another.  Typically this is used when an operation was started asynchronously.
- Spans have a status of unset, ok, or error.  Error means the operation had an error.  OK only happens if it was 
  explicitly set in the application, and is not necessary, as UNSET is typically interpreted as OK.
- Spans have a kind: client, server, producer, consumer, internal.  Gives it a hint on how to assembly them.  
  Producer and consumer spans may frequently not overlap in time, as consumer could be processing things asynchronously.

## Metrics

Meant to provide aggregate statistical information.

## PagerDuty

- **KTLO:** keep the lights on
- PD is best known for its incident management services.
- **Runbooks:** automate a task in response to some incident.
- There are users, teams.  Users can belong to multiple teams.  Users can have roles.  You can assign tags to users.
- There are schedules

## Logs

It’s best to write logs as events with attributes, like JSON records.  Fluentd is a daemon to gather and forward logs.  
Can be searched in ELK, Splunk, Google Cloud Logs, or in AWS CloudWatch.

- Splunk
- ELK
    - Elastic Search: enables complex searching.
    - Logstash: Consumes the data 
    - Kibana: for the display

## TODO

- Tracing
    - OpenCensus, OpenTelemetry
    - Jaeger
- DataDog
- NewRelic
- Cloud Metrics
