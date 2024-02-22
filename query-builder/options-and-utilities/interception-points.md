# Interception Points

Two interception points are available from QB: `preQBExecute` and `postQBExecute`. These fire before and after the `queryExecute` call, respectively.

## preQBExecute

The following information is available in the `interceptData` struct:

| Name         | Type   | Description                                             |
| ------------ | ------ | ------------------------------------------------------- |
| sql          | String | The SQL string to execute.                              |
| bindings     | Struct | The struct of bindings (keys and values) for the query. |
| options      | Struct | Any options to pass along to `queryExecute`.            |
| returnObject | String | The type to return: `query` or `result`.                |

## postQBExecute

The following information is available in the `interceptData` struct:

| Name         | Type          | Description                                             |
| ------------ | ------------- | ------------------------------------------------------- |
| sql          | String        | The SQL string to execute.                              |
| bindings     | Struct        | The struct of bindings (keys and values) for the query. |
| options      | Struct        | Any options to pass along to `queryExecute`.            |
| returnObject | String        | The type to return: `query` or `result`.                |
| query        | Query \| null | The query object or `null` if there isn't one.          |
| result       | Struct        | The query result struct.                                |
