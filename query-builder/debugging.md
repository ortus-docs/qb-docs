# Debugging

## Debugging a Single Query

### toSQL

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| showBindings | boolean | `false` | ​`false` | If true, the bindings for the query will be substituted back in where the question marks \(`?`\) appear. |

Returns the SQL that would be executed for the current query.

{% code title="QueryBuilder" %}
```javascript
var q = query.from( "users" )
    .where( "active", "=", 1 );

writeOutput( q.toSQL() );
```
{% endcode %}

{% code title="Result" %}
```sql
SELECT * FROM "users" WHERE "active" = ?
```
{% endcode %}

The bindings for the query are represented by question marks \(`?`\) just as when using `queryExecute`.  qb can replace each question mark with the corresponding `cfqueryparam`-compatible struct by passing `showBindings = true` to the method.

{% code title="QueryBuilder" %}
```javascript
var q = query.from( "users" )
    .where( "active", "=", 1 );

writeOutput( q.toSQL() );
```
{% endcode %}

{% code title="Result" %}
```sql
SELECT * FROM "users" WHERE "active" = {"value":1,"cfsqltype":"CF_SQL_NUMERIC","null":false}
```
{% endcode %}

If you want to show the SQL that would be executed for the `update`, `insert`, `updateOrInsert`, or `delete` methods, you can pass a `toSQL = true` flag to those methods.  Please see those individual methods for more information.

### tap

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| callback | Function | `true` | ​ | A function to execute with a clone of the current query. |

Executes a callback with a clone of the current query passed to it.  Any changes to the passed query is ignored and the original query returned.

While not strictly a debugging method, `tap` makes it easy to see the changes to a query after each call without introducing temporary variables.

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .tap( function( q ) {
        writeOutput( q.toSQL() & "<br>" );
    } )
    .where( "active", "=", 1 )
    .tap( function( q ) {
        writeOutput( q.toSQL() & "<br>" );
    } );
```
{% endcode %}

{% code title="Result" %}
```sql
SELECT * FROM "users"
SELECT * FROM "users" WHERE "active" = ?
```
{% endcode %}

## Debugging All Queries

### cbDebugger

Starting in [cbDebugger](https://forgebox.io/view/cbdebugger) 2.0.0 you can view all your qb queries for a request.  This is enabled by default if you have qb installed.  Make sure your debug output is configured correctly and scroll to the bottom of the page to find the debug output.

![](../.gitbook/assets/2020-05-04_12-40.png)

### LogBox Appender

qb is set to log all queries to a debug log out of the box.  To enable this behavior, configure LogBox to allow debug logging from qb's grammar classes.

{% code title="config/ColdBox.cfc" %}
```sql
logbox = {
    debug = [ "qb.models.Grammars" ]
};
```
{% endcode %}

{% hint style="info" %}
qb can be quite chatty when executing many database queries.  Make sure that this logging is only enabled for your development environments using [ColdBox's environment controls](https://coldbox.ortusbooks.com/getting-started/configuration/coldbox.cfc/configuration-directives/environments).
{% endhint %}

### ColdBox Interception Points

ColdBox Interception Points can also be used for logging, though you may find it easier to use LogBox.  See the documentation for [qb's Interception Points](options-and-utilities/interception-points.md) for more information.

