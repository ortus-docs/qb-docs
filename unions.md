## Unions

The query builder also provides a quick way to "union" two queries together. For example, you may create an initial query and use the `union` method to union it with a second query:
##### LARAVEL PHP EQUIVELANT
```

$first = DB::table('users')
->whereNull('first_name');

$users = DB::table('users')
->whereNull('last_name')
->union($first)
->get();
```
> {tip} The `unionAll` method is also available and has the same method signature as `union`.

