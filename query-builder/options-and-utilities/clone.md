# Clone

At times you may need to duplicate a query.  Using `clone` you have a performant way to duplicate a query without using the `duplicate` method.

```text
var q1 = query.from( "users" ).where( "firstName", "like", "Jo%" );
var q2 = q1.clone();
q2.getFrom(); // "users"
```



