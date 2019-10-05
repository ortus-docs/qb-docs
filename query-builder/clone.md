# Clone

At times you may need to duplicate a query.  Perhaps you need the count of all the records before paginating it.  Using `clone` you have a performant way to duplicate a query

```text
var q1 = query.from( "users" ).where( "firstName", "like", "Jo%" );
var q2 = q1.clone();
q2.getFrom(); // "users"
```



