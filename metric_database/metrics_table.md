# Creation Definition

```
CREATE TABLE IF NOT EXISTS metrics (
      id INTEGER PRIMARY KEY AUTOINCREMENT,
      timestamp BIGINT,
      node TEXT,
      sensor TEXT,
      value DOUBLE
   ))
```

`id` - Primary key

`timestamp` - Time metric was generated

`node` - The node id of sender

`sensor` - The sensor that recorded the metric

`value` - The value of the reading