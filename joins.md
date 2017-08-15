## Joins

#### Inner Join Clause

The query builder may also be used to write join statements. To perform a basic "inner join", you may use the `join` method on a query builder instance. The first argument passed to the `join` method is the name of the table you need to join to, while the remaining arguments specify the column constraints for the join. Of course, as you can see, you can join to multiple tables in a single query:
```
**need sample here**
```

##### LARAVEL PHP EQUIVELANT
```
$users = DB::table('users')
->join('contacts', 'users.id', '=', 'contacts.user_id')
->join('orders', 'users.id', '=', 'orders.user_id')
->select('users.*', 'contacts.phone', 'orders.price')
->get();
```

#### Left Join Clause

If you would like to perform a "left join" instead of an "inner join", use the `leftJoin` method. The `leftJoin` method has the same signature as the `join` method:
```
**need sample here**
```


##### LARAVEL PHP EQUIVELANT

```
$users = DB::table('users')
->leftJoin('posts', 'users.id', '=', 'posts.user_id')
->get();
```

#### Cross Join Clause

To perform a "cross join" use the `crossJoin` method with the name of the table you wish to cross join to. Cross joins generate a cartesian product between the first table and the joined table:
```
**need sample here**
```


##### LARAVEL PHP SAMPLE
```
$users = DB::table('sizes')
->crossJoin('colours')
->get();
```

#### Advanced Join Clauses

You may also specify more advanced join clauses. To get started, pass a `Closure` as the second argument into the `join` method. The `Closure` will receive a `JoinClause` object which allows you to specify constraints on the `join` clause:
```
**need sample here**
```

##### LARAVEL PHP EQUIVELANT

```
DB::table('users')
->join('contacts', function ($join) {
$join->on('users.id', '=', 'contacts.user_id')->orOn(...);
})
->get();
```

If you would like to use a "where" style clause on your joins, you may use the `where` and `orWhere` methods on a join. Instead of comparing two columns, these methods will compare the column against a value:
```
**need sample here**
```

##### LARAVEL PHP EQUIVELANT

```
DB::table('users')
->join('contacts', function ($join) {
$join->on('users.id', '=', 'contacts.user_id')
->where('contacts.user_id', '>', 5);
})
->get();
```