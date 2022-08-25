
## Formatting

Node V1 formatting is as follows:

```
{
  "id": "01d340e9-3841-4ab1-a6ca-c9b72d07528b",
  "description": "A node representing the whole house",
  "sensors": [
    {
      "id": "0000-0000-0000",
      "type": "temperature",
      "description": "Pantry temperature sensor"
    },
    {
      "id": "0000-0000-0001",
      "type": "humidity",
      "description": "Basement humidity sensor"
    }
  ]
}

```

## Rules 

1) Upper most ID field must contain a value. Non-unique IDs in registrar will be considered updates

2) Each sensor ID must be unique to the node in which they are contained. 