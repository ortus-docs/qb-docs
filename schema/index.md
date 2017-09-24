# Schema Builder

QB ships with a schema builder to help you build your database objects. This provides a few benefits:

+ The syntax is expressive and fluent, making it easy to understand what is being executed
+ The syntax is database-agnostic. Specific quirks are isolated in a Grammar file, making it easy to migrate between engines.

You start with a `SchemaBuilder` object.  The `SchemaBuilder` takes the same Grammar that a `QueryBuilder` takes.

```
// manually
var schema = new qb.models.schema.SchemaBuilder(
	new qb.models.grammars.MySQLGrammar()
);

// WireBox
var schema = wirebox.getInstance( "SchemaBuilder@qb" );
```

> Note: the `SchemaBuilder` is a transient, and a new one should be created for each operation.

The `SchemaBuilder` has four main methods to start your database object creation:

### [`create`](schema/create.md)

Create a new table in the database.

| Argument |   Type   | Required | Default |                                             Description                                             |
|----------|----------|----------|---------|-----------------------------------------------------------------------------------------------------|
| table    | string   | `true`   |         | The name of the table to create.                                                                    |
| callback | function | `true`   |         | A callback function used to define the table body. It is passed a `Blueprint` as the only argument. |
| options  | struct   | `false`  | `{}`    | OPtions to pass to `queryExecute`.                                                                  |
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
} );
```

### [`alter`](schema/alter.md)

Alter an existing table in the database.

| Argument |   Type   | Required | Default |                                             Description                                             |
|----------|----------|----------|---------|-----------------------------------------------------------------------------------------------------|
| table    | string   | `true`   |         | The name of the table to alter.                                                                     |
| callback | function | `true`   |         | A callback function used to define the changes to the table. It is passed a `Blueprint` as the only argument. |
| options  | struct   | `false`  | `{}`    | Options to pass to `queryExecute`.                                                                  |
| execute  | boolean  | `false`  | `true`  | Run the query immediately after building it.                                                        |

In addition to using the  [fields](schema/fields.md) and [indexes](schema/indexes.md) off of the passed-in `Blueprint` object, the `Blueprint` contains helpers such as `addConstraint`, `removeConstraint`, `addColumn`, `renameColumn`, and `dropColumn` to assist in altering existing tables.

Example:
```
schema.alter( "users", function( table ) {
	table.addConstraint( table.unique( "username" ) );
	table.dropColumn( "last_logged_in" );
} );
```

### [`drop` and `dropIfExists`](schema/drop.md)

Drop a table from the database.

| Argument |   Type  | Required | Default |                 Description                  |
|----------|---------|----------|---------|----------------------------------------------|
| table    | string  | `true`   |         | The name of the table to drop.               |
| options  | struct  | `false`  | `{}`    | Options to pass to `queryExecute`.           |
| execute  | boolean | `false`  | `true`  | Run the query immediately after building it. |

In addition to using the  [fields](schema/fields.md) and [indexes](schema/indexes.md) off of the passed-in `Blueprint` object, the `Blueprint` contains helpers such as `addConstraint`, `removeConstraint`, `addColumn`, `renameColumn`, and `dropColumn` to assist in altering existing tables.

Example:
```
schema.drop( "user_logins" );
```

## Additionally, there are a few utility methods defined on `SchemaBuilder` as well:

### `rename`

Rename a table from an old name to a new name

| Argument |   Type  | Required | Default |                 Description                  |
|----------|---------|----------|---------|----------------------------------------------|
| from     | string  | `true`   |         | The old table name.                          |
| to       | string  | `true`   |         | The new table name.                          |
| options  | struct  | `false`  | `{}`    | Options to pass to `queryExecute`.           |
| execute  | boolean | `false`  | `true`  | Run the query immediately after building it. |

Example:
```
schema.rename( "posts", "blog_posts" );
```


### `hasTable`

Check if a table exists in the database.

| Argument |   Type  | Required | Default |                 Description                  |
|----------|---------|----------|---------|----------------------------------------------|
| name     | string  | `true`   |         | The name of the table to check.              |
| options  | struct  | `false`  | `{}`    | Options to pass to `queryExecute`.           |
| execute  | boolean | `false`  | `true`  | Run the query immediately after building it. |

Example:
```
schema.hasTable( "users" );
```

### `hasColumn`

Check if a column exists in a table in the database.

| Argument |   Type  | Required | Default |                    Description                    |
|----------|---------|----------|---------|---------------------------------------------------|
| table    | string  | `true`   |         | The name of the table to check for the column in. |
| column   | string  | `true`   |         | The column to check for in the table.             |
| options  | struct  | `false`  | `{}`    | Options to pass to `queryExecute`.                |
| execute  | boolean | `false`  | `true`  | Run the query immediately after building it.      |

Example:
```
schema.hasColumn( "users", "last_logged_in" );
```
