### Parameter Grouping

Sometimes you may need to create more advanced where clauses such as "where exists" clauses or nested parameter groupings. The Quick query builder can handle these as well. To get started, let's look at an example of grouping constraints within parenthesis:

DB::table('users')
->where('name', '=', 'John')
->orWhere(function ($query) {
$query->where('votes', '>', 100)
->where('title', '<>', 'Admin');
})
->get();

As you can see, passing a `Closure` into the `orWhere` method instructs the query builder to begin a constraint group. The `Closure` will receive a query builder instance which you can use to set the constraints that should be contained within the parenthesis group. The example above will produce the following SQL:

select * from users where name = 'John' or (votes > 100 and title <> 'Admin')

