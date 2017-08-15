### Aggregates

The query builder also provides a variety of aggregate methods such as `count`, `max`, `min`, and `sum`. You may call any of these methods after constructing your query:

```
var getAllResults = query.from('users').count();
writeDump(getAllResults);
```


```
var getAllResults = query.from('users').max('age');
writeDump(getAllResults);
```

```
var getAllResults = query.from('users').min('age');
writeDump(getAllResults);
```


Of course, you may combine these methods with other clauses:

```
var getResults = query.from('users')
.where('active', '=', 1)
.max('age');
writeDump(getResults);
```



