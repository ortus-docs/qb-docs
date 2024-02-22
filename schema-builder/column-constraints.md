# Column Constraints

A `TableIndex` can be created directly from a [`Blueprint`](creating-table-constraints.md) or from a existing [`Column`](column-modifiers.md). The `TableIndex` includes methods for further configuring the index which is required when defining foreign keys.

## references

Set the referencing column for a foreign key relationship. For example, `id` for a `country_id` column.

| Argument | Type | Required | Default | Description                                                             |
| -------- | ---- | -------- | ------- | ----------------------------------------------------------------------- |
| columns  | any  | `true`   |         | A column or array of columns that represents the foreign key reference. |

**Example:**

**SchemaBuilder**

```
schema.create( "users", function( table ) {
    table.unsignedInteger( "country_id" ).references( "id" ).onTable( "countries" );
} );
```

**SQL (MySQL)**

```sql
CREATE TABLE `users` (
    `country_id` INTEGER UNSIGNED NOT NULL,
    CONSTRAINT `fk_users_country_id` FOREIGN KEY (`country_id`) REFERENCES `countries` (`id`) ON UPDATE NO ACTION ON DELETE NO ACTION
)
```

## onTable

Sets the referencing table for a foreign key relationship. For example, `countries` for a `country_id` column.

| Argument | Type   | Required | Default | Description                 |
| -------- | ------ | -------- | ------- | --------------------------- |
| table    | string | `true`   |         | The referencing table name. |

**Example:**

**SchemaBuilder**

```
schema.create( "users", function( table ) {
    table.unsignedInteger( "country_id" ).references( "id" ).onTable( "countries" );
} );
```

**SQL (MySQL)**

```sql
CREATE TABLE `users` (
    `country_id` INTEGER UNSIGNED NOT NULL,
    CONSTRAINT `fk_users_country_id` FOREIGN KEY (`country_id`) REFERENCES `countries` (`id`) ON UPDATE NO ACTION ON DELETE NO ACTION
)
```

## onUpdate

Set the strategy for updating foreign keys when the parent key is updated.

| Argument | Type   | Required | Default | Description                                                                                    |
| -------- | ------ | -------- | ------- | ---------------------------------------------------------------------------------------------- |
| option   | string | `true`   |         | The strategy to use. Available values are: RESTRICT, CASCADE, SET NULL, NO ACTION, SET DEFAULT |

**Example:**

**SchemaBuilder**

```
schema.create( "users", function( table ) {
    table.unsignedInteger( "country_id" )
        .references( "id" )
        .onTable( "countries" )
        .onUpdate( "CASCADE" );
} );
```

**SQL (MySQL)**

```sql
CREATE TABLE `users` (
    `country_id` INTEGER UNSIGNED NOT NULL,
    CONSTRAINT `fk_users_country_id` FOREIGN KEY (`country_id`) REFERENCES `countries` (`id`) ON UPDATE CASCADE ON DELETE NO ACTION
)
```

## onDelete

Set the strategy for updating foreign keys when the parent key is deleted.

| Argument | Type   | Required | Default | Description                                                                                    |
| -------- | ------ | -------- | ------- | ---------------------------------------------------------------------------------------------- |
| option   | string | `true`   |         | The strategy to use. Available values are: RESTRICT, CASCADE, SET NULL, NO ACTION, SET DEFAULT |

**Example:**

**SchemaBuilder**

```
schema.create( "users", function( table ) {
    table.unsignedInteger( "country_id" )
        .references( "id" )
        .onTable( "countries" )
        .onDelete( "SET NULL" );
} );
```

**SQL (MySQL)**

```sql
CREATE TABLE `users` (
    `country_id` INTEGER UNSIGNED NOT NULL,
    CONSTRAINT `fk_users_country_id` FOREIGN KEY (`country_id`) REFERENCES `countries` (`id`) ON UPDATE NO ACTION ON DELETE SET NULL
)
```
