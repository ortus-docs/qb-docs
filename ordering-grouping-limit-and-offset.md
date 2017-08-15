## Ordering, Grouping, Limit, & Offset

#### orderBy

The `orderBy` method allows you to sort the result of the query by a given column. The first argument to the `orderBy` method should be the column you wish to sort by, while the second argument controls the direction of the sort and may be either `asc` or `desc`:
##### LARAVEL PHP EQUIVELANT
```

$users = DB::table('users')
->orderBy('name', 'desc')
->get();
```

#### latest / oldest

The `latest` and `oldest` methods allow you to easily order results by date. By default, result will be ordered by the `created_at` column. Or, you may pass the column name that you wish to sort by:

$user = DB::table('users')
->latest()
->first();

#### inRandomOrder

The `inRandomOrder` method may be used to sort the query results randomly. For example, you may use this method to fetch a random user:

$randomUser = DB::table('users')
->inRandomOrder()
->first();

#### groupBy / having / havingRaw

The `groupBy` and `having` methods may be used to group the query results. The `having` method's signature is similar to that of the `where` method:

$users = DB::table('users')
->groupBy('account_id')
->having('account_id', '>', 100)
->get();

The `havingRaw` method may be used to set a raw string as the value of the `having` clause. For example, we can find all of the departments with sales greater than $2,500:

$users = DB::table('orders')
->select('department', DB::raw('SUM(price) as total_sales'))
->groupBy('department')
->havingRaw('SUM(price) > 2500')
->get();

#### skip / take

To limit the number of results returned from the query, or to skip a given number of results in the query, you may use the `skip` and `take` methods:

$users = DB::table('users')->skip(10)->take(5)->get();

Alternatively, you may use the `limit` and `offset` methods:

$users = DB::table('users')
->offset(10)
->limit(5)
->get();

