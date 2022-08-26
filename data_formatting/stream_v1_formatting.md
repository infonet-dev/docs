## Formatting

Strea, V1 formatting is as follows:

```
{
  "timestamp": 1661534969,
  "sequence": 0,
  "data": [
       {
          "timestamp": 1661463750264,
          "node_id": "asoidj-askdaskl-aslkdmasd-asda",
          "sensor_id": "aksldma-asdasdas-asdasd-asdasd",
          "value": 0.0123
       },
       {
          "timestamp": 1661463750274,
          "node_id": "asoidj-askdaskl-aslkdmasd-asda",
          "sensor_id": "aksldma-asdasdas-asdasd-asdasd",
          "value": 0.0123
       }
  ]
}
```


## Rules 

1) Timestamp non-zero

2) Data list not empty (at least one metric)

3) Each metric in data must follow `reading_v1` formatting rules