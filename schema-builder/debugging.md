# Debugging

### pretend

A `SchemaBuilder` instance can be put into pretend mode by calling the `pretend` method.  In this mode, the `SchemaBuilder` will turn all query operations into no-ops. A log of the SQL that would have been executed can be retrieved from the query log.

Once a `SchemaBuilder` instance has been set to pretend mode, it cannot be unset.  Instead, you will need to obtain a new `SchemaBuilder` instance.

### queryLog

Each instance of a `SchemaBuilder` maintains a log of queries it executed.  This can be accessed by calling `getQueryLog`. This will return an array of structs like so:

```json
[
  {
    "sql": "CREATE TABLE `users` (`id` INT PRIMARY KEY AUTO_INCREMENT, `email` VARCHAR NOT NULL)",
    "bindings": [],
    "options": { "datasource": "main" },
    "returnObject": "array",
    "pretend": false,
    "result": {},
    "executionTime": 21
  }
]
```

This can be very useful in combination with the [`pretend`](debugging.md#pretend) feature to see what SQL will be executed before actually executing it.
