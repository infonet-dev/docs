# Monolith

Starting monolith is simple:

```
./monolith configs/standard.toml
```

To get a demonstration of monolith run it along with `tools/device_emulator` and optionally `tools/stream_receiver`.
For examples on how to create a node endpoint that submits metric data to monolith take a look at the device emulator, and if you're interested in having live metrics streamed to you check out the stream receiver.

# Settings Configuration


Example of a configuration file : 

```
[monolith]
instance_name = "monoloth_standard"
log_file_name = "monolith"
registration_db_path = "/tmp/monolith_registration.db"
metric_db_path = "/tmp/monolith_metrics.db"

[networking]
use_ipv6 = false
ipv4_address = "0.0.0.0"
ipv6_address = "::1"
http_port = 8080

[rules]
rule_script = "rules/default.lua"

[alert]
max_alert_sends = 0
alert_cooldown_seconds = 40.0

[twilio]
account_sid = ""
auth_token = ""
from = ""
to = ""
```

## [monolith]
- required


`instance_name` - The name of the application to seperate it from other monoliths running
`log_file_name` - The name of the file to log to (exclude `.log`)
`registration_db_path` - Path to where registration database exists / will be made
`metric_db_path` - Path to where metric database exists / will be made

## [networking]
- required


`use_ipv6` - Boolean to enable ipv6 (not yet implemented)
`ipv4_address` - IP V4 Address to bind to 
`ipv6_address` - IP V6 Address to bind to
`http_port` - Port to serve all HTTP endpoints on

## [rules]
- required

`rule_script` - Path to the lua file containing method for handling stream reactions

## [alert]
`max_alert_sends` - Maximum number of alerts allowed to be sent (0 = infinite)
`alert_cooldown_seconds` - Number of seconds between sends per given alert id

## [twilio]
- optional

`account_sid` - SID for twilio account
`auth_token` - Auth token for twilio account
`from` - Twilio phone number (including the `+` prefix)
`to` - Destination phone number (including the `+` prefix)


# Rules Script

```
-- This is an example of the function required to receive metric readings directly from the
-- rule_executor that is submitted via the data_submission service. This method is called 
-- almost as frequently as metrics are received by the server
function accept_reading_v1_from_monolith(timestamp, node_id, sensor_id, value)

   print(" ts:" .. timestamp .. ", node:" .. node_id .. ", sensor:" .. sensor_id .. ", value:" .. value)

   -- Alert with id 0
   monolith_trigger_alert(0, "Alert message")

   -- Dispatch an action request to a (controller-id, action-id, value)
   monolith_dispatch_action("fire_extinguisher", "toggle_extinguisher", 1.0)
end
```

The rules script is a lua script that must contain the function `accept_reading_v1_from_monolith` in the global scope. This function is what is called by the alert manager object so we can have configurable alerting. This same object provides a function call to `monolith_trigger_alert` which takes in an integer (id) and a string (message) to trigger SMS messages (iff configured via twilio configuration). Each classification of alert (user decided) should ideally share the same ID. The ID allows the alert system to filter alerts and ensure that the configured receivers of alerts are not brutaly spammed. 

For a more detailed rules file check out the `demu.lua` file in the monolith repo to see the rules set up to be used with the `device_emulator` found in the `tools` repo.


# HTTP Endpoints

## Version Info

`/version`

Returns a `version_v1_formatting` json data structure containing build hash, and semantic versioning info.

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

**Add metric stream receiver**

 `/metric/stream/add/<ip>/<port>`
 
**Delete metric stream receiver**

 `/metric/stream/delete/<ip>/<port>`

Once an endpoint is setup to receive steams the server will send formatted stream data to the endpoint matching "stream_v1_formatting." Data is sent as such: `4` bytes are sent to indicate length of encoded stream followed by the encoded data. It is recommended that `crate::networking::message_server` is used to receive streams.

**Submitting Metrics (sensor readings)**

`/metric/submit/<encoded_data>`

*Note*: 
Data encoding must match "reading_v1_formatting" - See docs/data_formatting for details

Metrics containing a node or sensor id that has not been registered by a registrar endpoint (above) will be ignored and dropped.

**Submitting Metrics (heartbeats)**

There are two ways heartbeats are submitted
Method 1: HTTP 

`/metric/heartbeat/<encoded_heartbeat>`

Method 2: 

Just send a metric. Any time a metric is submitted its id is given to the heartbeat manager to indicate that 
the sender is alive!

**Retrieving Metrics**

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