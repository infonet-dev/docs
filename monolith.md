# Monolith

## Settings

`http_port` - Http access to stream/device registration

`metric_submission_port` - Port that takes in encoded sensor reports 

# HTTP Endpoints

## Metric

**Submit metric data**

`/metric/submit/<encoded_data>`

Data encoding must match "reading_v1_formatting" - See docs/data_formatting for details

**Add metric stream receiver**

 `/metric/stream/add/<ip>/<port>`
 
**Delete metric stream receiver**

 `/metric/stream/delete/<ip>/<port>`

Once an endpoint is setup to receive steams the server will send formatted stream data to the endpoint matching "stream_v1_formatting." Data is sent as such: `4` bytes are sent to indicate length of encoded stream followed by the encoded data. It is recommended that `crate::networking::message_server` is used to receive streams.

## Registrar

**Register**

`/registrar/add/<id>/<encoded_node | encoded_controller>`

Data encoding must match "node_v1_formatting" or "controller_v1_formatting" - See docs/data_formatting for details

**De-Register**

`/registrar/delete/<id>`

**Fetch**

`/registrar/fetch/<id>`

**Probe**

`/registrar/probe/<id>`

# Submitting Metrics (sensor readings)

There are two ways to submit data

Method 1: HTTP 

`/metric/submit/<encoded_data>`

Method 2: 

TCP Using a `crate::message_writer` directed at the configured `metric_submission_port` port,
submit an encoded `reading_v1`.

*Note*: 

Metrics containing a node or sensor id that has not been registered by a registrar endpoint will be ignored and dropped.

# Submitting Metrics (heartbeats)
There are three ways heartbeats are submitted
Method 1: HTTP 

`/metric/heartbeat/<encoded_heartbeat>`

Method 2: 

TCP Using a `crate::message_writer` directed at the configured `metric_submission_port` port, 
submit an encoded `heartbeat_v1`.

Method 2: 

Just send a metric. Any time a metric is submitted its id is given to the heartbeat manager to indicate that 
the sender is alive!
