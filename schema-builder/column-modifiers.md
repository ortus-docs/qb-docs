# Column Modifiers

When [creating a column](columns.md) from the `Blueprint` object, a `Column` object is returned. This `column` gives you access to a few modifier commands to further configure the column.

| Methods |  |
| :--- | :--- |
| [comment](column-modifiers.md#comment) | [default](column-modifiers.md#default) |
| [nullable](column-modifiers.md#nullable) | [primaryKey](column-modifiers.md#primaryKey) |
| [references](column-modifiers.md#references) | [unsigned](column-modifiers.md#unsigned) |
| [unique](column-modifiers.md#unique) |  |

## comment

Attach a comment to the column.

| Argument | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| comment | string | `true` |  | The comment text. |

**Example:**

**SchemaBuilder**

```javascript
schema.create( "users", function( table ) {
    table.integer( "age" ).comment( "Do not lie about your age" );
} );
```

**SQL \(MySQL\)**

```sql
CREATE TABLE `users` (
    `age` INTEGER NOT NULL COMMENT `Do not lie about your age`
)
```

## default

Sets a default value for the column.

**Note:** The value is not escaped, allowing you to specify functions like `NOW()` or literals like `1`. To specify a literal string, wrap the value in quotes.

| Argument | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| value | string | `true` |  | The default value. |

**Example:**

**SchemaBuilder**

```text
schema.create( "users", function( table ) {
    table.boolean( "is_active" ).default( 1 );
    table.timestamp( "created_date" ).default( "NOW()" );
    table.string( "country" ).default( "'USA'" );
} );
```

**SQL \(MySQL\)**

```sql
CREATE TABLE `users` (
    `is_active` TINYINT(1) DEFAULT 1,
    `created_date` TIMESTAMP DEFAULT NOW(),
    `country` VARCHAR(255) DEFAULT 'USA'
)
```

## nullable

Sets the column to allow null values.

| Argument | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| No arguments |  |  |  |  |

All columns are created as `NOT NULL` by default. As such, there is no `notNull` method.

**Example:**

**SchemaBuilder**

```text
schema.create( "users", function( table ) {
    table.timestamp( "last_logged_in" ).nullable()
} );
```

**SQL \(MySQL\)**

```sql
CREATE TABLE `users` (
    `last_logged_in` TIMESTAMP
)
```

## primaryKey

Adds the column as a primary key for the table.

| Argument | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| indexName | string | `false` | A derived name built from the table name and column name. | The name to use for the primary key constraint. |

The `primaryKey` method returns a [`TableIndex` instance.](https://github.com/ortus/qb/tree/b0b49b9b35032508e73231da3a39856a7bc9d21b/schema/schema/indexes.md) Additional methods can be chained off of it.

**Example:**

**SchemaBuilder**

```text
schema.create( "users", function( table ) {
    table.uuid( "id" ).primaryKey();
} );
```

**SQL \(MySQL\)**

```sql
CREATE TABLE `users` (
    `id` CHAR(35) NOT NULL,
    CONSTAINT `pk_users_id` PRIMARY KEY (`id`)
)
```

## references

Creates a foreign key constraint for the column.

| Argument | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| value | string | `true` |  | The default value. |

**IMPORTANT:** Additional configuration of the foreign constraint is done by calling methods on the returned [`TableIndex` instance.](https://github.com/ortus/qb/tree/b0b49b9b35032508e73231da3a39856a7bc9d21b/schema/schema/indexes.md)

**Example:**

**SchemaBuilder**

```text
schema.create( "users", function( table ) {
    table.unsignedInteger( "country_id" ).references( "id" ).onTable( "countries" ).onDelete( "cascade" );
} );
```

**SQL \(MySQL\)**

```sql
CREATE TABLE `users` (
    `country_id` INTEGER UNSIGNED NOT NULL,
    CONSTRAINT `fk_users_country_id` FOREIGN KEY (`country_id`) REFERENCES `countries` (`id`) ON UPDATE NO ACTION ON DELETE CASCADE
)
```

## unsigned

Sets the column as unsigned.

| Argument | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| No arguments |  |  |  |  |

**Example:**

**SchemaBuilder**

```text
schema.create( "users", function( table ) {
    table.integer( age" ).unsigned();
} );
```

**SQL \(MySQL\)**

```sql
CREATE TABLE `users` (
    `age` INTEGER UNSIGNED NOT NULL
)
```

## unique

Sets the column to have the UNIQUE constraint.

| Argument | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| No arguments |  |  |  |  |

**Example:**

**SchemaBuilder**

```text
schema.create( "email", function( table ) {
    table.string( email" ).unique();
} );
```

**SQL \(MySQL\)**

```sql
CREATE TABLE `users` (
    `email` VARCHAR(255) NOT NULL UNIQUE
)
```

