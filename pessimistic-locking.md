## Pessimistic Locking

The query builder also includes a few functions to help you do "pessimistic locking" on your `select` statements. To run the statement with a "shared lock", you may use the `sharedLock` method on a query. A shared lock prevents the selected rows from being modified until your transaction commits:

##### LARAVEL PHP EQUIVELANT
```
DB::table('users')->where('votes', '>', 100)->sharedLock()->get();
```
Alternatively, you may use the `lockForUpdate` method. A "for update" lock prevents the rows from being modified or from being selected with another shared lock:

##### LARAVEL PHP EQUIVELANT
```
DB::table('users')->where('votes', '>', 100)->lockForUpdate()->get();
```