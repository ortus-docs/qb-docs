### Increment & Decrement

The query builder also provides convenient methods for incrementing or decrementing the value of a given column. This is simply a shortcut, providing a more expressive and terse interface compared to manually writing the `update` statement.

Both of these methods accept at least one argument: the column to modify. A second argument may optionally be passed to control the amount by which the column should be incremented or decremented:

DB::table('users')->increment('votes');

DB::table('users')->increment('votes', 5);

DB::table('users')->decrement('votes');

DB::table('users')->decrement('votes', 5);

You may also specify additional columns to update during the operation:

DB::table('users')->increment('votes', 1, ['name' => 'John']);

