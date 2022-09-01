# Monolith

## Settings

`http_port` - Http access to stream/device registration

`metric_submission_port` - Port that takes in encoded sensor reports 

# HTTP Endpoints

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

## Metric

**Submit metric data**

`/metric/submit/<encoded_data>`

Data encoding must match "reading_v1_formatting" - See docs/data_formatting for details

**Add metric stream receiver**

 `/metric/stream/add/<ip>/<port>`
 
**Delete metric stream receiver**

 `/metric/stream/delete/<ip>/<port>`

Once an endpoint is setup to receive steams the server will send formatted stream data to the endpoint matching "stream_v1_formatting." Data is sent as such: `4` bytes are sent to indicate length of encoded stream followed by the encoded data. It is recommended that `crate::networking::message_server` is used to receive streams.

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

# Retrieving Metrics 

`/metric/fetch/nodes`

Retrieve all nodes that have submitted data. Result is a json list of node ids

Example:
```
   {"status":200,"data":["node_0","node_1"]}
```

`/metric/fetch/< node id >/sensors`

Retrive all sensors from a given node that have reported data

Example:
```
   {"status":200,"data":["8e697aaf-f9d7-4a9f-89d8-d32ac8772d2e","e2eee2b8-4a0e-4c5e-9283-2a9927d3c86d","96192aef-25f2-4876-ac73-581b05b42cd9","0eb89b53-70d0-4d56-baf7-505e9eaa81bf","a6e8131d-1e7f-47b6-a720-78d30832135b","bbffb71f-c909-46af-9ae2-370478b028ad"]}
```

`/metric/fetch/< node id >/range/< start time >/< end time >`

Retrieve all sensor readings for a node between a start time and an end time. All time ranges
are UNIX timestamps (seconds)

Reponses will be in an http json (like above) with a comma seperated list of `reading_v1_formatting` encoded readings.

`/metric/fetch/< node id >/before/< time >`

Retrieve all sensor readings for a node before a time. Time range
is UNIX timestamp (seconds)

Reponses will be in an http json (like above) with a comma seperated list of `reading_v1_formatting` encoded readings.

`/metric/fetch/< node id >/after/< time >`

Retrieve all sensor readings for a node after a time. Time range
is UNIX timestamp (seconds)

Reponses will be in an http json (like above) with a comma seperated list of `reading_v1_formatting` encoded readings.