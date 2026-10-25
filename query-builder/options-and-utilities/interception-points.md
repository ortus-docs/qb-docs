# Interception Points

Three interception points are available from qb: `preQBExecute` runs before execution, `postQBExecute` runs after success or a pretend execution, and `onQBExecuteException` runs when `queryExecute` throws. The optional [query telemetry adapter](../debugging/query-telemetry.md) manages these listeners for compatible telemetry clients.

## preQBExecute

The following information is available in the `interceptData` struct:

| Name         | Type   | Description                                             |
| ------------ | ------ | ------------------------------------------------------- |
| sql          | String | The SQL string to execute.                              |
| bindings     | Array or Struct | The bindings passed to query execution. |
| options      | Struct | Any options to pass along to `queryExecute`.            |
| returnObject | String | The type to return: `query` or `result`.                |
| pretend      | Boolean | Whether this execution is in pretend mode. |

## postQBExecute

The following information is available in the `interceptData` struct:

| Name         | Type          | Description                                             |
| ------------ | ------------- | ------------------------------------------------------- |
| sql          | String        | The SQL string to execute.                              |
| bindings     | Array or Struct | The bindings passed to query execution. |
| options      | Struct        | Any options to pass along to `queryExecute`.            |
| returnObject | String        | The type to return: `query` or `result`.                |
| query        | Query \| null | The query object or `null` if there isn't one.          |
| result       | Struct        | The query result struct.                                |
| pretend      | Boolean | Whether this execution is in pretend mode. |
| executionTime | Numeric | Elapsed execution time in milliseconds. |

## onQBExecuteException

This point fires when an actual `queryExecute` call fails, including failures the calling application catches. It does not fire for pretend queries or for errors in pre/post interceptors or post-processing callbacks. A failed query does not announce `postQBExecute`.

The following information is available in the `interceptData` struct:

| Name | Type | Description |
| --- | --- | --- |
| sql | String | The SQL passed to query execution, including transformations made by pre-execution listeners. |
| bindings | Array or Struct | The bindings passed to query execution. |
| options | Struct | The query execution options. |
| returnObject | String | The requested return type: `query` or `result`. |
| pretend | Boolean | Always `false` for this interception point. |
| executionTime | Numeric | Elapsed execution time in milliseconds. |
| exception | Exception | The original query exception. |
| query | null | No query result is available. |
| result | Struct | An empty result struct. |

qb rethrows the original query exception unchanged after notifying observers. If an observer throws, qb reports that observer failure through its debug logger. Neither an observer failure nor a failure in that diagnostic logger replaces the original query exception. Like the existing points, this hook uses `announce` when available and falls back to `processState` for older interceptor services.

For observability, correlate this hook with the execution started by `preQBExecute` and finish its existing span as failed. When native runtime instrumentation already owns that execution, use this hook only as supplemental failure coverage to avoid duplicate spans. Do not export the entire interception data: SQL must be scrubbed, and bindings, results, credentials, and sensitive query options must remain private.

```cfml
component {
    property name="log" inject="logbox:logger:{this}";

    function onQBExecuteException( event, interceptData ) {
        // Keep bindings, options, and query contents out of diagnostics.
        variables.log.debug(
            "Query execution failed after #interceptData.executionTime# ms"
        );
    }
}
```
