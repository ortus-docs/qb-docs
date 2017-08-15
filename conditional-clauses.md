## Conditional Clauses

Sometimes you may want clauses to apply to a query only when something else is true. For instance you may only want to apply a `where` statement if a given input value is present on the incoming request. You may accomplish this using the `when` method:

$role = $request->input('role');

$users = DB::table('users')
->when($role, function ($query) use ($role) {
return $query->where('role_id', $role);
})
->get();


The `when` method only executes the given Closure when the first parameter is `true`. If the first parameter is `false`, the Closure will not be executed.

You may pass another Closure as the third parameter to the `when` method. This Closure will execute if the first parameter evaluates as `false`. To illustrate how this feature may be used, we will use it to configure the default sorting of a query:

$sortBy = null;

$users = DB::table('users')
->when($sortBy, function ($query) use ($sortBy) {
return $query->orderBy($sortBy);
}, function ($query) {
return $query->orderBy('name');
})
->get();


