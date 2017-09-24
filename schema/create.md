# Create

This method allows you to create a table object.

| Argument |   Type   | Required | Default |                                             Description                                             |
|----------|----------|----------|---------|-----------------------------------------------------------------------------------------------------|
| table    | string   | `true`   |         | The name of the table to create.                                                                    |
| callback | function | `true`   |         | A callback function used to define the table body. It is passed a `Blueprint` as the only argument. |
| options  | struct   | `false`  | `{}`    | Options to pass to `queryExecute`.                                                                  |
| execute  | boolean  | `false`  | `true`  | Run the query immediately after building it.                                                        |

The majority of the work comes from calling methods on the `Blueprint` object.  A `Blueprint` defines the [fields](schema/fields.md) and [indexes](schema/indexes.md) for your tables.

Example:
```
schema.create( "users", function( table ) {
	table.increments( "id" );
	table.string( "email" );
	table.string( "password" );
	table.timestamp( "created_date" );
	table.timestamp( "modified_date" );
	table.timestamp( "last_logged_in" ).nullable();
} );
```

This would convert to the following SQL in MySQL:
```sql
CREATE TABLE `users` (
	`id` INTEGER(10) UNSIGNED NOT NULL AUTO_INCREMENT,
	`email` VARCHAR(255) NOT NULL,
	`password` VARCHAR(255) NOT NULL,
	`created_date` TIMESTAMP NOT NULL,
	`modified_date` TIMESTAMP NOT NULL,
	`last_logged_in` TIMESTAMP,
	CONSTRAINT `pk_users_id` PRIMARY KEY (`id`)
)
```

Only one table can be created at a time.  If you wanted to create multiple tables, you would call `create` multiple times.

The `callback` argument is where you define the schema of your table.  It is passed a `Blueprint` object.  This is commonly aliased as `table` in the callback.  `Blueprint` defines the field, index and constraint methods to build your table.  You can find a comprehensive list of all available methods here for [fields](schema/fields) and here for [indexes and constraints](schema/indexes).
