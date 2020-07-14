# Query Options

Each query execution method allows for the passing of an options struct. This is the same struct you would pass to [`queryExecute`](https://cfdocs.org/queryexecute).

## Default Options

qb allows you to specify default options when creating the QueryBuilder instance using the `defaultOptions` argument. You can combine this with WireBox to create custom QueryBuilder instances pointing to different datasources and even different grammars.

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
    .setGrammar( wirebox.getInstance( "MSSQLGrammar@qb" ) );
```
{% endcode %}

