### JSON Where Clauses

Quick also supports querying JSON column types on databases that provide support for JSON column types. Currently, this includes MySQL 5.7 and Postgres. To query a JSON column, use the `->` operator:

$users = DB::table('users')
->where('options->language', 'en')
->get();

$users = DB::table('users')
->where('preferences->dining->meal', 'salad')
->get();

