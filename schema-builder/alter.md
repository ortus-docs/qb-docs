# Alter

The alter method loads up an existing table in order to make modifications. These modifications may include adding, renaming, or dropping columns and constraints.

To begin altering an existing table, call the `alter` method off of the `SchemaBuilder`. This method takes a callback as the second parameter that is passed a `Blueprint` object, much like the [`create`](create.md) method.

| Argument | Type     | Required | Default | Description                                                                                                       |
| -------- | -------- | -------- | ------- | ----------------------------------------------------------------------------------------------------------------- |
| table    | string   | `true`   |         | The name of the table to alter.                                                                                   |
| callback | function | `true`   |         | A callback function used to define the alterations to the table. It is passed a `Blueprint` as the only argument. |
| options  | struct   | `false`  | `{}`    | Options to pass to `queryExecute`.                                                                                |
| execute  | boolean  | `false`  | `true`  | Run the query immediately after building it.                                                                      |

> Calling multiple methods inside a single `alter` callback creates multiple SQL statements to be executed. qb takes care of this execution for you by default.

The following methods off of `Blueprint` let you modify the table inside the callback:

## addColumn

Add a new column to an existing table. Takes a `Column` instance as the only argument.

Any instance of `Column` is valid like those returned by the [column methods](columns.md) (`integer`, `string`, etc.) as well as the [column modifier methods](column-modifiers.md) (`unsigned`, `nullable`, etc.).

| Argument | Type     | Required | Default | Description                          |
| -------- | -------- | -------- | ------- | ------------------------------------ |
| column   | `Column` | `true`   |         | A column object to add to the table. |

**Example:**

**SchemaBuilder**

```javascript
schema.alter( "users", function( table ) {
    table.addColumn( table.boolean( "is_active" ) );
} );
```

**SQL (MySQL)**

```sql
ALTER TABLE `users` ADD `is_active` TINYINT(1) NOT NULL
```

## raw

An escape hatch to directly insert any sql in to the statement.

| Argument | Type   | Required | Default | Description                                    |
| -------- | ------ | -------- | ------- | ---------------------------------------------- |
| sql      | string | `true`   |         | The sql to insert directly into the statement. |

**Example:**

**SchemaBuilder**

```javascript
schema.alter( "registrars", function ( table ) {
    table.addColumn(
        table.raw( "HasDNSSecAPI bit NOT NULL CONSTRAINT DF_registrars_HasDNSSecAPI DEFAULT (0)" )
    );
} );
```

**SQL (MySQL)**

```sql
ALTER TABLE `registrars`
ADD HasDNSSecAPI bit NOT NULL
CONSTRAINT DF_registrars_HasDNSSecAPI DEFAULT (0)
```

## dropColumn

Drop a column on an existing table.

| Argument | Type   | Required | Default | Description                     |
| -------- | ------ | -------- | ------- | ------------------------------- |
| name     | string | `true`   |         | The name of the column to drop. |

**Example:**

**SchemaBuilder**

```javascript
schema.alter( "users", function( table ) {
    table.dropColumn( "username" );
} );
```

**SQL (MySQL)**

```sql
ALTER TABLE `users` DROP COLUMN `username`
```

## modifyColumn

Modify an existing column on a table.

| Argument | Type     | Required | Default | Description                                  |
| -------- | -------- | -------- | ------- | -------------------------------------------- |
| name     | string   | `true`   |         | The name of the column to modify.            |
| column   | `Column` | `true`   |         | A column object to replace the named column. |

**Example:**

**SchemaBuilder**

```javascript
schema.alter( "users", function( table ) {
    table.modifyColumn( "name", table.string( "username" ) );
} );
```

**SQL (MySQL)**

```sql
ALTER TABLE `users` CHANGE `name` `username` VARCHAR(255) NOT NULL
```

## renameColumn

Rename a column on a table. A full `Column` instance is required as the second argument for Grammars that need to redeclare the column definition when renaming.

| Argument | Type     | Required | Default | Description                                              |
| -------- | -------- | -------- | ------- | -------------------------------------------------------- |
| name     | string   | `true`   |         | The current name of a column.                            |
| column   | `Column` | `true`   |         | A column object with the new column name and definition. |

**Example:**

**SchemaBuilder**

```javascript
schema.alter( "users", function( table ) {
    table.renameColumn( "name", table.string( "username" ) );
} );
```

**SQL (MySQL)**

```sql
ALTER TABLE `users` CHANGE `name` `username` VARCHAR(255) NOT NULL
```

## addConstraint

Add an index or key to an existing table. Any `TableIndex` instance is valid, like those created by the [index methods](broken-reference) (`unique`, `index`, `primaryKey`, etc.).

| Argument   | Type         | Required | Default | Description                                    |
| ---------- | ------------ | -------- | ------- | ---------------------------------------------- |
| constraint | `TableIndex` | `true`   |         | The `TableIndex` instance to add to the table. |

**Example:**

**SchemaBuilder**

```javascript
schema.alter( "users", function( table ) {
    table.addConstraint( table.unique( "username" ) );
} );
```

**SQL (MySQL)**

```sql
ALTER TABLE `users` ADD CONSTRAINT `unq_users_username` UNIQUE (`username`)
```

## dropConstraint

Drop an existing table constraint.

| Argument | Type                   | Required | Default | Description                                                                                                               |
| -------- | ---------------------- | -------- | ------- | ------------------------------------------------------------------------------------------------------------------------- |
| name     | string OR `TableIndex` | `true`   |         | The name of the constraint to drop. You can alternatively pass a `TableIndex` instance to use the dynamic name generated. |

**Example:**

**SchemaBuilder**

```javascript
schema.alter( "users", function( table ) {
    table.dropConstraint( "unq_users_full_name" );
    table.dropConstraint( table.unique( "username" ) );
} );
```

**SQL (MySQL)**

```sql
ALTER TABLE `users` DROP INDEX `unq_users_full_name`
ALTER TABLE `users` DROP INDEX `unq_users_username`
```

## renameConstraint

Rename an existing table constraint.

| Argument | Type                   | Required | Default | Description                                                                                                                                |
| -------- | ---------------------- | -------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| oldName  | string OR `TableIndex` | `true`   |         | The old or current name of the constraint to rename. You can alternatively pass a `TableIndex` instance to use the dynamic name generated. |
| newName  | string OR `TableIndex` | `true`   |         | The new name of the constraint.  You can alternatively pass a `TableIndex` instance to use the dynamic name generated.                     |

**Example:**

**SchemaBuilder**

```javascript
schema.alter( "users", function( table ) {
    table.renameConstraint( "unq_users_first_name_last_name", "unq_users_full_name" );
} );
```

**SQL (MySQL)**

```sql
ALTER TABLE `users` RENAME INDEX `unq_users_first_name_last_name` TO `unq_users_full_name`
```

## renameTable

Rename an existing table.

| Argument | Type   | Required | Default | Description                                     |
| -------- | ------ | -------- | ------- | ----------------------------------------------- |
| oldName  | string | `true`   |         | The old or current name of the table to rename. |
| newName  | string | `true`   |         | The new name of the table.                      |

**Example:**

**SchemaBuilder**

```javascript
schema.renameTable( "workers", "employees" );
```

**SQL (MySQL)**

```sql
RENAME TABLE `workers` TO `employees`
```

## rename

An alias for `renameTable`.

| Argument | Type   | Required | Default | Description                                     |
| -------- | ------ | -------- | ------- | ----------------------------------------------- |
| oldName  | string | `true`   |         | The old or current name of the table to rename. |
| newName  | string | `true`   |         | The new name of the table.                      |

**Example:**

**SchemaBuilder**

```javascript
schema.rename( "workers", "employees" );
```

**SQL (MySQL)**

```sql
RENAME TABLE `workers` TO `employees`
```
