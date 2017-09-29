# Creating Table Constraints

Sometimes you want to add constraints on a table level, rather than a column level.  The following methods will let you accomplish that.

## foreignKey

Create a foreign key constraint from one or more columns.  Follow up this call with calls to the `TableIndex`'s [`references`](schema/indexes.md#references) and [`onTable`](schema/indexes.md#onTable) methods.

| Argument |       Type      | Required |                              Default                              |                                  Description                                   |
|----------|-----------------|----------|-------------------------------------------------------------------|--------------------------------------------------------------------------------|
| columns  | string or array | `true`   |                                                                   | The column or array of columns that references a key or keys on another table. |
| name     | string          | `false`  | A generated name consisting of the table name and column name(s). | The name of the foreign key constraint.                                        |

**Example:**

__SchemaBuilder__
```
schema.create( "users", function( table ) {
	table.unsignedInteger( "country_id" );
	table.foreignKey( "country_id" ).references( "id" ).onTable( "countries" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `users` (
	`country_id` INTEGER UNSIGNED NOT NULL,
	CONSTRAINT `fk_users_country_id` FOREIGN KEY (`country_id`) REFERENCES `countries` (`id`) ON UPDATE NO ACTION ON DELETE NO ACTION
)
```

## index

Create a generic index from one or more columns.

| Argument |       Type      | Required |                              Default                              |                      Description                       |
|----------|-----------------|----------|-------------------------------------------------------------------|--------------------------------------------------------|
| columns  | string or array | `true`   |                                                                   | The column or array of columns that make up the index. |
| name     | string          | `false`  | A generated name consisting of the table name and column name(s). | The name of the index constraint.                      |
| 

**Example:**

__SchemaBuilder__
```
schema.create( "users", function( table ) {
	table.string( "first_name" );
	table.string( "last_name" );
	table.index( [ "first_name", "last_name" ], "idx_users_full_name" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `users` (
	`first_name` VARCHAR(255) NOT NULL,
	`last_name` VARCHAR(255) NOT NULL,
	INDEX `idx_users_full_name` (`first_name`, `last_name`)
)
```

## primaryKey

Create a primary key constraint from one or more columns.

| Argument |       Type      | Required |                              Default                              |                         Description                          |
|----------|-----------------|----------|-------------------------------------------------------------------|--------------------------------------------------------------|
| columns  | string or array | `true`   |                                                                   | The column or array of columns that make up the primary key. |
| name     | string          | `false`  | A generated name consisting of the table name and column name(s). | The name of the primary key constraint.                      |
| 

**Example:**

__SchemaBuilder__
```
schema.create( "posts_users", function( table ) {
	table.unsignedInteger( "post_id" ).references( "id" ).onTable( "posts" );
	table.unsignedInteger( "user_id" ).references( "id" ).onTable( "users" );
	table.primaryKey( [ "post_id", "user_id" ], "pk_posts_users" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `posts_users` (
	`post_id` VARCHAR(255) NOT NULL,
	`user_id` VARCHAR(255) NOT NULL,
	INDEX `idx_users_full_name` (`first_name`, `last_name`),
	CONSTRAINT `fk_posts_users_post_id` FOREIGN KEY (`post_id`) REFERENCES `posts` (`id`) ON UPDATE NO ACTION ON DELETE NO ACTION,
	CONSTRAINT `fk_posts_users_user_id` FOREIGN KEY (`user_id`) REFERENCES `users` (`id`) ON UPDATE NO ACTION ON DELETE NO ACTION,
	CONSTRAINT ""pk_users_first_name_last_name"" PRIMARY KEY (""first_name"", ""last_name"")
)
```

## unique

Create a unique constraint from one or more columns.

| Argument |       Type      | Required |                              Default                              |                                  Description                                   |
|----------|-----------------|----------|-------------------------------------------------------------------|--------------------------------------------------------------------------------|
| columns  | string or array | `true`   |                                                                   | The column or array of columns that make up the unique constraint. |
| name     | string          | `false`  | A generated name consisting of the table name and column name(s). | The name of the unique constraint.                                        |

**Example:**

__SchemaBuilder__
```
schema.create( "users", function( table ) {
	table.increments( "id" );
	table.string( "username ");
	table.unique( "username" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `users` (
	`id` INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
	UNIQUE (`username`)
)
```
