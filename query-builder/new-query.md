# Getting a New Query

A query builder is a stateful, transient object. That means that if you want to execute two different queries, you need two separate instances of `QueryBuilder`.

{% code title="QueryBuilder" %}
```javascript
// This will cause you pain and grief...

var user = query.from( "users" )
  .where( "username", rc.username )
  .first();

var posts = query.from( "posts" ).get();
// This will error because `username` is not a column in `posts`.
```
{% endcode %}

As such, be careful when injecting QueryBuilder in to a component.  If the component is a singleton, you will need to create the QueryBuilder inline or use a provider.  This applies to ColdBox handlers as well.

{% code title="handlers/posts.cfc" %}
```javascript
component {

    property name="query" inject="QueryBuilder@qb";
   
    function create( event, rc, prc ) {
        query.table( "posts" )
            .where( "id", rc.id )
            .update( event.getOnly( [ "body" ] ) );
    }

}
```
{% endcode %}

While the above may seem innoculous, it can run in to issues as multiple requests come in to your application.  Each request is sharing the same query builder instance and subsequent requests will have unintended results as the `where` clause keeps growing request after request.

The solution is to either create the QueryBuilder inline, ensuring that each request has its own query to execute:

{% code title="handlers/posts.cfc" %}
```javascript
component {
   
    function create( event, rc, prc ) {
        getInstance( "QueryBuilder@qb" )
            .table( "posts" )
            .where( "id", rc.id )
            .update( event.getOnly( [ "body" ] ) );
    }

}
```
{% endcode %}

Or to use a WireBox provider to create a new query each time it is accessed:

{% code title="handlers/posts.cfc" %}
```javascript
component {

    property name="query" inject="provider:QueryBuilder@qb";
   
    function create( event, rc, prc ) {
        query.table( "posts" )
            .where( "id", rc.id )
            .update( event.getOnly( [ "body" ] ) );
    }

}
```
{% endcode %}

{% hint style="warning" %}
One caveat when using a WireBox Provider: WireBox Providers proxy methods on to a new instance of the provided mapping on all methods except `get`.  `get` is a method on the Provider itself.  If you call `get` as the first method on a Provider it will return a new instance of QueryBuilder, not execute the query.  In those \(rare\) cases you will need to call `query.get().get()`.
{% endhint %}

### newQuery

Once you have access to a QueryBuilder instance, you can create a new query using the same `grammar`, `utils`, `returnFormat`, `paginationCollector`, `columnFormatter`, `parentQuery`, and `defaultOptions` as the current QueryBuilder instance.

```javascript
// This will cause you pain and grief...

var user = query.from( "users" )
  .where( "username", rc.username )
  .first();

var posts = query.newQuery().from( "posts" ).get();
// This will work as we expect it to.
```

