## Raw Expressions
**_I need to verify this information:_**
Sometimes you may need to use a raw expression in a query. These expressions will be injected into the query as strings, so be careful not to create any SQL injection points! To create a raw expression, you may use the `addSelect` method:

```
var getResults = query.from('users').where('age','>=','18');
getResults = getResults.selectRaw('name as fullName,email,age').get();
writeDump(getResults);

```





