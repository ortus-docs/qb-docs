# Overview

QB ships with a schema builder to help you build your database objects. This provides a few benefits:

* The syntax is expressive and fluent, making it easy to understand what is being executed
* The syntax is database-agnostic. Specific quirks are isolated in a Grammar file, making it easy to migrate between engines.

You start with a `SchemaBuilder` object. The `SchemaBuilder` takes the same Grammar that a `QueryBuilder` takes.

```text
// manually
var schema = new qb.models.schema.SchemaBuilder(
    new qb.models.grammars.MySQLGrammar()
);

// WireBox
var schema = wirebox.getInstance( "SchemaBuilder@qb" );
```

> Note: the `SchemaBuilder` is a transient, and a new one should be created for each operation.

The `SchemaBuilder` has four main methods to start your database object creation:

### [`create`](https://github.com/ortus/qb/tree/b0b49b9b35032508e73231da3a39856a7bc9d21b/schema/schema/create.md)

Create a new table in the database.

| Argument | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| table | string | `true` |  | The name of the table to create. |
| callback | function | `true` |  | A callback function used to define the table body. It is passed a `Blueprint` as the only argument. |
| options | struct | `false` | `{}` | Options to pass to `queryExecute`. |
| execute | boolean | `false` | `true` | Run the query immediately after building it. |

The majority of the work comes from calling methods on the `Blueprint` object. A `Blueprint` defines the [columns](https://github.com/ortus/qb/tree/b0b49b9b35032508e73231da3a39856a7bc9d21b/schema/schema/columns.md) and [indexes](https://github.com/ortus/qb/tree/b0b49b9b35032508e73231da3a39856a7bc9d21b/schema/schema/indexes.md) for your tables.

**Example:**

**SchemaBuilder**

```javascript
schema.create( "users", function( table ) {
    table.increments( "id" );
    table.string( "email" );
    table.string( "password" );
    table.timestamp( "created_date" ).nullable();
    table.timestamp( "modified_date" ).nullable();
} );
```

**SQL \(MySQL\)**

```sql
CREATE TABLE `users` (
    `id` INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
    `email` VARCHAR(255) NOT NULL,
    `password` VARCHAR(255) NOT NULL,
    `created_date` TIMESTAMP,
    `modified_date` TIMESTAMP,
    CONSTRAINT `pk_users_id` PRIMARY KEY (`id`)
)
```

### [`alter`](https://github.com/ortus/qb/tree/b0b49b9b35032508e73231da3a39856a7bc9d21b/schema/schema/alter.md)

Alter an existing table in the database.

| Argument | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| table | string | `true` |  | The name of the table to alter. |
| callback | function | `true` |  | A callback function used to define the changes to the table. It is passed a `Blueprint` as the only argument. |
| options | struct | `false` | `{}` | Options to pass to `queryExecute`. |
| execute | boolean | `false` | `true` | Run the query immediately after building it. |

In addition to using the [columns](https://github.com/ortus/qb/tree/b0b49b9b35032508e73231da3a39856a7bc9d21b/schema/schema/columns.md) and [indexes](https://github.com/ortus/qb/tree/b0b49b9b35032508e73231da3a39856a7bc9d21b/schema/schema/indexes.md) off of the passed-in `Blueprint` object, the `Blueprint` contains helpers such as `addConstraint`, `removeConstraint`, `addColumn`, `renameColumn`, and `dropColumn` to assist in altering existing tables.

**Example:**

**SchemaBuilder**

```javascript
schema.alter( "users", function( table ) {
    table.addConstraint( table.unique( "username" ) );
    table.dropColumn( "last_logged_in" );
} );
```

**SQL \(MySQL\)**

```sql
ALTER TABLE `users` ADD CONSTRAINT `unq_users_username` UNIQUE (`username`);
ALTER TABLE `users` DROP COLUMN `last_logged_in`;
```

### [`drop` and `dropIfExists`](https://github.com/ortus/qb/tree/b0b49b9b35032508e73231da3a39856a7bc9d21b/schema/schema/drop.md)

Drop a table from the database.

| Argument | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| table | string | `true` |  | The name of the table to drop. |
| options | struct | `false` | `{}` | Options to pass to `queryExecute`. |
| execute | boolean | `false` | `true` | Run the query immediately after building it. |

**Example:**

**SchemaBuilder**

```javascript
schema.drop( "user_logins" );
```

**SQL \(MySQL\)**

```sql
DROP TABLE `user_logins`
```

## Additionally, there are a few utility methods defined on `SchemaBuilder` as well:

### `rename`

Rename a table from an old name to a new name

| Argument | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| from | string | `true` |  | The old table name. |
| to | string | `true` |  | The new table name. |
| options | struct | `false` | `{}` | Options to pass to `queryExecute`. |
| execute | boolean | `false` | `true` | Run the query immediately after building it. |

**Example:**

**SchemaBuilder**

```javascript
schema.rename( "posts", "blog_posts" );
```

**SQL \(MySQL\)**

```sql
RENAME TABLE `posts` TO `blog_posts`
```

### `hasTable`

Check if a table exists in the database.

| Argument | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| name | string | `true` |  | The name of the table to check. |
| options | struct | `false` | `{}` | Options to pass to `queryExecute`. |
| execute | boolean | `false` | `true` | Run the query immediately after building it. |

**Example:**

**SchemaBuilder**

```javascript
schema.hasTable( "users" );
```

**SQL \(MySQL\)**

```sql
SELECT 1
FROM `information_schema`.`tables`
WHERE `table_name` = 'users'
```

### `hasColumn`

Check if a column exists in a table in the database.

| Argument | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| table | string | `true` |  | The name of the table to check for the column in. |
| column | string | `true` |  | The column to check for in the table. |
| options | struct | `false` | `{}` | Options to pass to `queryExecute`. |
| execute | boolean | `false` | `true` | Run the query immediately after building it. |

**Example:**

**SchemaBuilder**

```javascript
schema.hasColumn( "users", "last_logged_in" );
```

**SQL \(MySQL\)**

```sql
SELECT 1
FROM `information_schema`.`columns`
WHERE `table_name` = 'users'
    AND `column_name` = 'last_logged_in'
```

