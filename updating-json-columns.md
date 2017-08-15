### Updating JSON Columns

When updating a JSON column, you should use `->` syntax to access the appropriate key in the JSON object. This operation is only supported on databases that support JSON columns:

DB::table('users')
->where('id', 1)
->update(['options->enabled' => true]);

