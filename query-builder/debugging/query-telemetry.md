# Query Telemetry

Use `QueryTelemetryAdapter@qb` to trace qb query executions through a compatible telemetry client. Available in qb 14.2.0.

Nothing is enabled by default. qb does not install a telemetry client, require Sentry, or choose where traces are sent. Your client controls sampling, SQL scrubbing, and delivery.

## Sentry Setup

{% hint style="info" %}
This example requires Sentry's configurable query adapter support, which is still proposed in [Sentry PR #56](https://github.com/coldbox-modules/sentry/pull/56). The qb adapter is independent of that Sentry release.
{% endhint %}

Add these settings to your existing Sentry configuration:

```cfscript
// Select the optional query adapter shipped by qb.
moduleSettings.sentry.queryAdapter = "QueryTelemetryAdapter@qb";

// Record this technical database system on the query spans.
moduleSettings.sentry.databaseSystem = "postgresql";
```

Keep your client's DSN, tracing, and sampling settings. Selecting the adapter does not enable capture or send traces by itself.

The adapter observes qb's [execution interception points](../options-and-utilities/interception-points.md). It starts a span before a query runs and finishes it after success or failure, including failures your application catches. Pretend queries are skipped. The adapter does not trace database calls that bypass qb.

## Adapter Contract

The WireBox mapping exposes `configure(context)`. You can also configure it directly with any telemetry object that implements the methods below:

```cfscript
adapter = wirebox.getInstance( "QueryTelemetryAdapter@qb" );
registration = adapter.configure( {
    // Required telemetry object; it owns scopes, spans, SQL scrubbing, and delivery.
    telemetry: telemetryClient,
    // Optional technical database-system name; defaults to "other".
    databaseSystem: "postgresql"
} );

// At application shutdown, stop new observations; running queries still finish.
registration.shutdown();
```

| Method | Contract |
| --- | --- |
| `getScope()` | Return the current execution scope as a struct. |
| `setScope(scope)` | Activate a scope for the current execution. |
| `scrubSQL(sql)` | Return SQL suitable for a span name, with sensitive values removed. |
| `startSpan(name,operation,attributes)` | Return a span handle. The adapter supplies the scrubbed SQL, `db.query`, and the `db.system` attribute. |
| `span.finish(status)` | Finish the span with `ok` or `internal_error`. |

`configure()` returns a struct containing `mode: "custom"` and a `shutdown` callback. An optional `applicationName` in the setup context is accepted but is not exported. The injected interceptor service already belongs to the application.

For standalone usage, pass a compatible interceptor service to `new qb.models.Telemetry.QueryTelemetryAdapter(interceptorService)`. It must provide `registerInterceptor(interceptorObject,interceptorName,customPoints)` and `unregister(name)`, and it must be the same service used by your query grammar.

## Scope and Duplicate Spans

Nested qb executions restore their enclosing scopes. Each execution completes once, and observer failures do not replace the query result or original database exception.

The adapter honors the `manualQuerySpan` scope marker. A compatible manual query wrapper owns its span, so the adapter skips that execution. While tracing a qb query, the adapter sets the marker so compatible native listeners skip it too. Its own nested qb executions can still create child spans.

Native engine tracing is an alternative to this adapter. Automatic de-duplication requires the integrations to share this scope-marker contract.

Only scrubbed SQL, the span operation, and the configured database-system attribute are passed to telemetry. Bindings, query options, results, and exception contents are not exported by the adapter.

## Shutdown and Timeouts

Each configuration owns a separate listener registration. `shutdown()` is idempotent and returns immediately. It stops new observations, keeps completion hooks registered for queries already observed, and unregisters only its own listeners once those queries finish.

Shutdown does not cancel database calls or enforce a query timeout. Configure timeouts through your engine or driver's [query options](../options-and-utilities/query-options.md). A query that never finishes can keep its completion hooks registered.
