## Inserts

The query builder also provides an `insert` method for inserting records into the database table. The `insert` method accepts an array of column names and values:

##### LARAVEL PHP EQUIVELANT
```
DB::table('users')->insert(
['email' => 'john@example.com', 'votes' => 0]
);
```
You may even insert several records into the table with a single call to `insert` by passing an array of arrays. Each array represents a row to be inserted into the table:

##### LARAVEL PHP EQUIVELANT
```
DB::table('users')->insert([
['email' => 'taylor@example.com', 'votes' => 0],
['email' => 'dayle@example.com', 'votes' => 0]
]);
```
#### Auto-Incrementing IDs

If the table has an auto-incrementing id, use the `insertGetId` method to insert a record and then retrieve the ID:

##### LARAVEL PHP EQUIVELANT
```
$id = DB::table('users')->insertGetId(
['email' => 'john@example.com', 'votes' => 0]
);
```

