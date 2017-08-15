### Where Exists Clauses

The `whereExists` method allows you to write `where exists` SQL clauses. The `whereExists` method accepts a `Closure` argument, which will receive a query builder instance allowing you to define the query that should be placed inside of the "exists" clause:

DB::table('users')
->whereExists(function ($query) {
$query->select(DB::raw(1))
->from('orders')
->whereRaw('orders.user_id = users.id');
})
->get();

The query above will produce the following SQL:

select * from users
where exists (
select 1 from orders where orders.user_id = users.id
)

