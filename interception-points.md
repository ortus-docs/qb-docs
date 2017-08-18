## Interception Points

Two interception points are available from QB: `preQBExecute` and `postQBExecute`.  These fire before and after the `queryExecute` call, respectively.  The following information is available in the `interceptData` struct:

| Name | Type | Description |
| --- | --- | --- |
| sql | String | The sql string to execute | 
| bindings | Struct | The struct of bindings (keys and values) for the query |
| options | Struct | Any options to pass along to `queryExecute` |

