## Inserts

The query builder also provides an `insert` method for inserting records into the database table. The `insert` method accepts an array of column names and values:

DB::table('users')->insert(
['email' => 'john@example.com', 'votes' => 0]
);

You may even insert several records into the table with a single call to `insert` by passing an array of arrays. Each array represents a row to be inserted into the table:

DB::table('users')->insert([
['email' => 'taylor@example.com', 'votes' => 0],
['email' => 'dayle@example.com', 'votes' => 0]
]);

#### Auto-Incrementing IDs

If the table has an auto-incrementing id, use the `insertGetId` method to insert a record and then retrieve the ID:

$id = DB::table('users')->insertGetId(
['email' => 'john@example.com', 'votes' => 0]
);

> {note} When using PostgreSQL the insertGetId method expects the auto-incrementing column to be named `id`. If you would like to retrieve the ID from a different "sequence", you may pass the sequence name as the second parameter to the `insertGetId` method.

