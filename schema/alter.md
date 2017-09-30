# alter

The alter method loads up an existing table in order to make modifications.  These modifications may include adding, renaming, or dropping columns and constraints.

To begin altering an existing table, call the `alter` method off of the `SchemaBuilder`.  This method takes a callback as the second parameter that is passed a `Blueprint` object, much like the [`create`](schema/create.md) method.

| Argument |   Type   | Required | Default |                                                    Description                                                    |
|----------|----------|----------|---------|-------------------------------------------------------------------------------------------------------------------|
| table    | string   | `true`   |         | The name of the table to alter.                                                                                   |
| callback | function | `true`   |         | A callback function used to define the alterations to the table. It is passed a `Blueprint` as the only argument. |
| options  | struct   | `false`  | `{}`    | Options to pass to `queryExecute`.                                                                                |
| execute  | boolean  | `false`  | `true`  | Run the query immediately after building it.                                                                      |

> Calling multiple methods inside a single `alter` callback creates multiple SQL statements to be executed.  qb takes care of this execution for you by default. 

The following methods off of `Blueprint` let you modify the table inside the callback:

## addColumn

Add a new column to an existing table.  Takes a `Column` instance as the only argument.

Any instance of `Column` is valid like those returned by the [column methods](schema/columns.md) (`integer`, `string`, etc.) as well as the [column modifier methods](schema/column-modifiers) (`unsigned`, `nullable`, etc.).

| Argument |   Type   | Required | Default |             Description              |
|----------|----------|----------|---------|--------------------------------------|
| column   | `Column` | `true`   |         | A column object to add to the table. |

**Example:**

__SchemaBuilder__
```js
schema.alter( "users", function( table ) {
	table.addColumn( table.boolean( "is_active" ) );
} );
```

__SQL (MySQL)__
```sql
ALTER TABLE `users` ADD `is_active` TINYINT(1) NOT NULL
```

## dropColumn

Drop a column on an existing table.

| Argument |  Type  | Required | Default |           Description           |
|----------|--------|----------|---------|---------------------------------|
| name     | string | `true`   |         | The name of the column to drop. |

**Example:**

__SchemaBuilder__
```js
schema.alter( "users", function( table ) {
	table.dropColumn( "username" );
} );
```

__SQL (MySQL)__
```sql
ALTER TABLE `users` DROP COLUMN `username`
```

## modifyColumn

Modify an existing column on a table.

| Argument |   Type   | Required | Default |                 Description                  |
|----------|----------|----------|---------|----------------------------------------------|
| name     | string   | `true`   |         | The name of the column to modify.            |
| column   | `Column` | `true`   |         | A column object to replace the named column. |

**Example:**

__SchemaBuilder__
```js
schema.alter( "users", function( table ) {
    table.modifyColumn( "name", table.string( "username" ) );
} );
```

__SQL (MySQL)__
```sql
ALTER TABLE `users` CHANGE `name` `username` VARCHAR(255) NOT NULL
```

## renameColumn

Rename a column on a table.  A full `Column` instance is required as the second argument for Grammars that need to redeclare the column definition when renaming.

| Argument |   Type   | Required | Default |                       Description                        |
|----------|----------|----------|---------|----------------------------------------------------------|
| name     | string   | `true`   |         | The current name of a column.                            |
| column   | `Column` | `true`   |         | A column object with the new column name and definition. |

**Example:**

__SchemaBuilder__
```js
schema.alter( "users", function( table ) {
	table.renameColumn( "name", table.string( "username" ) );
} );
```

__SQL (MySQL)__
```sql
ALTER TABLE `users` CHANGE `name` `username` VARCHAR(255) NOT NULL
```

## addConstraint

Add an index or key to an existing table. Any `TableIndex` instance is valid, like those created by the [index methods](schema/indexes.md) (`unique`, `index`, `primaryKey`, etc.).

|  Argument  |     Type     | Required | Default |                  Description                   |
|------------|--------------|----------|---------|------------------------------------------------|
| constraint | `TableIndex` | `true`   |         | The `TableIndex` instance to add to the table. |

**Example:**

__SchemaBuilder__
```js
schema.alter( "users", function( table ) {
    table.addConstraint( table.unique( "username" ) );
} );
```

__SQL (MySQL)__
```sql
ALTER TABLE `users` ADD CONSTRAINT `unq_users_username` UNIQUE (`username`)
```

## dropConstraint

Drop an existing table constraint.

| Argument |          Type          | Required | Default |                                                        Description                                                        |
|----------|------------------------|----------|---------|---------------------------------------------------------------------------------------------------------------------------|
| name     | string OR `TableIndex` | `true`   |         | The name of the constraint to drop. You can alternatively pass a `TableIndex` instance to use the dynamic name generated. |

**Example:**

__SchemaBuilder__
```js
schema.alter( "users", function( table ) {
	table.dropConstraint( "unq_users_full_name" );
    table.dropConstraint( table.unique( "username" ) );
} );
```

__SQL (MySQL)__
```sql
ALTER TABLE `users` DROP INDEX `unq_users_full_name`
ALTER TABLE `users` DROP INDEX `unq_users_username`
```

## renameConstraint

Rename an existing table constraint.

| Argument |          Type          | Required | Default |                                                                Description                                                                 |
|----------|------------------------|----------|---------|--------------------------------------------------------------------------------------------------------------------------------------------|
| oldName  | string OR `TableIndex` | `true`   |         | The old or current name of the constraint to rename. You can alternatively pass a `TableIndex` instance to use the dynamic name generated. |
| newName  | string OR `TableIndex` | `true`   |         | The new name of the constraint.  You can alternatively pass a `TableIndex` instance to use the dynamic name generated.                     |

**Example:**

__SchemaBuilder__
```js
schema.alter( "users", function( table ) {
	table.renameConstraint( "unq_users_first_name_last_name", "unq_users_full_name" );
} );
```

__SQL (MySQL)__
```sql
ALTER TABLE `users` RENAME INDEX `unq_users_first_name_last_name` TO `unq_users_full_name`
```
