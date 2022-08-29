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

`/registrar/node/add/<id>/<encoded_node>`

Data encoding must match "node_v1_formatting" - See docs/data_formatting for details

**De-Register**

`/registrar/node/delete/<id>`

# Submitting Metrics (sensor readings)

There are two ways to submit data

Method 1: HTTP 

`/metric/submit/<encoded_data>`

Method 2: 

TCP Using a `crate::message_writer` directed at the configured `metric_submission_port` port. 

*Note*: 

Metrics containing a node or sensor id that has not been registered by a registrar endpoint will be ignored and dropped.