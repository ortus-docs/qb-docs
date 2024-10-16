# Query Options and Utilities

Each query execution method allows for the passing of an options struct. This is the same struct you would pass to [`queryExecute`](https://cfdocs.org/queryexecute).

## Default Options

qb allows you to specify default options when creating the QueryBuilder instance using the `defaultOptions` argument.

You can set `defaultOptions` for the default QueryBuilder (`QueryBuilder@qb`) in your `config/ColdBox.cfc` file under `moduleSettings`.

```javascript
moduleSettings = {
    "qb": {
        "defaultOptions": {
            "timeout": 60
        }
    }
};
```

You can also combine this with WireBox to create custom QueryBuilder instances pointing to different datasources and even different grammars.

{% hint style="info" %}
When mapping to components provided by modules, such as qb, use the [`afterAspectsLoad`](https://coldbox.ortusbooks.com/digging-deeper/interceptors/core-interception-points/application-life-cycle-events) interception point inside your `config/WireBox.cfc` to ensure all modules are fully loaded and available.
{% endhint %}

{% code title="config/WireBox.cfc" %}
```javascript
component {

    function afterAspectsLoad() {
        binder.map( "MyCustomQueryBuilder" )
            .to( "qb.models.Query.QueryBuilder" )
            .initArg( name = "grammar", ref = "AutoDiscover@qb" )
            .initArg( name = "defaultOptions", value = {
                "datasource": "my_custom_datasource" 
            } );
    }

}
```
{% endcode %}

## Retrieving results from alternative datasources

In `Application.cfc` you can specify your default datasource which will be used by qb. If you want to retrieve data from other datasources you can specify this in all retrieval functions by using the extra options parameter such as:

{% code title="QueryBuilder" %}
```javascript
query.from( "users" )
    .get( options = { datasource: "MyOtherDatasourceName" } );
```
{% endcode %}

If you also want to use a non-default SQL Grammar you have to specify this when creating your `QueryBuilder`.

{% code title="QueryBuilder" %}
```javascript
var query = wirebox.getInstance( "QueryBuilder@qb" )
    .setGrammar( wirebox.getInstance( "SqlServerGrammar@qb" ) );
```
{% endcode %}

## Replacing or Inlining Bindings

qb can inline the query bindings into the SQL string that it has built up.  This is used by other tools like [`toSQL`](../debugging/#tosql) or [`dump`](../debugging/#dump) to provide a richer debugging experience.  It is also publicly available for other libraries to use, such as [CommandBox Migrations](https://forgebox.io/view/commandbox-mgirations).

### replaceBindings

Replace the question marks (?) in a sql string with the bindings provided.

| Name     | Type            | Required | Default Value | Description                                                                                                                                      |
| -------- | --------------- | -------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| sql      | `String`        | true     |               | The SQL with question marks (`?`) to replace with bindings.                                                                                      |
| bindings | `Array<Struct>` | true     |               | The bindings to use when replacing the question marks (`?`) in the provided SQL string.                                                          |
| inline   | `boolean`       | false    | `false`       | Flag to inline the bindings value or not.  If `true`, a SQL-executable value will be replaced.  If `false`, the binding struct will be replaced. |
