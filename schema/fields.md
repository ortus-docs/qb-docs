# Fields

The `Blueprint` object has many field types available to construct your table schema.

> The converted SQL below will be for the `MySQLGrammar`.


## `bigIncrements`

Create an auto-incrementing column using an unsigned `BIGINT` type.  This column is also set as the primary key for the table.

|  Argument |  Type  | Required | Default |                                                                   Description                                                                   |
|-----------|--------|----------|---------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| name      | string | `true`   |         | The name for the column.                                                                                                                         |
| indexName | string | `false`  |         | The name for the primary key index.  If no name is passed in, the name will be dynamically created based off of the table name and column name. |

#### Example:

##### SchemaBuilder
```
schema.table( "users", function( table ) {
	table.bigIncrements( "id" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `users` (
	`id` BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
	CONSTRAINT `pk_users_id` PRIMARY KEY (`id`)
)
```

## `bigInteger`

Create a column using a `BIGINT` equivalent type for your database.

|  Argument |   Type  | Required | Default |          Description          |
|-----------|---------|----------|---------|-------------------------------|
| name      | string  | `true`   |         | The name for the column.      |
| precision | numeric | `false`  |         | The precision for the column. |

#### Example (no precision):

##### SchemaBuilder
```
schema.table( "users", function( table ) {
	table.bigInteger( "salary" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `users` (
	`salary` BIGINT NOT NULL
)
```

#### Example (with precision):

##### SchemaBuilder
```
schema.table( "users", function( table ) {
	table.bigInteger( "salary", 5 );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `users` (
	`salary` BIGINT(5) NOT NULL
)
```

## `bit`

Create a column using a `BIT` equivalent type for your database.  The length can be specified as the second argument.

| Argument |   Type  | Required | Default |        Description         |
|----------|---------|----------|---------|----------------------------|
| name     | string  | `true`   |         | The name for the column.   |
| length   | numeric | `false`  |       1 | The length for the column. |

#### Example (default length):

##### SchemaBuilder
```
schema.table( "users", function( table ) {
	table.bit( "is_active" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `users` (
	`is_active` BIT(1) NOT NULL
)
```

#### Example (custom length):

##### SchemaBuilder
```
schema.table( "users", function( table ) {
	table.bit( "is_active", 2 );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `users` (
	`is_active` BIT(2) NOT NULL
)
```

## `boolean`

Create a column using a `BOOLEAN` equivalent type for your database.

| Argument |  Type  | Required | Default |       Description        |
|----------|--------|----------|---------|--------------------------|
| name     | string | `true`   |         | The name for the column. |

#### Example:

##### SchemaBuilder
```
schema.table( "users", function( table ) {
	table.boolean( "is_subscribed" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `users` (
	`is_subscribed` TINYINT(1) NOT NULL
)
```

## `char`

Create a column using a `CHAR` equivalent type for your database.

| Argument |   Type  | Required | Default |        Description         |
|----------|---------|----------|---------|----------------------------|
| name     | string  | `true`   |         | The name for the column.   |
| length   | numeric | `false`  |       1 | The length for the column. |

#### Example (default length):

##### SchemaBuilder
```
schema.table( "students", function( table ) {
	table.char( "grade" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `students` (
	`grade` CHAR(1) NOT NULL
)
```

#### Example (custom length):

##### SchemaBuilder
```
schema.table( "users", function( table ) {
	table.char( "tshirt_size", 4 );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `users` (
	`tshirt_size` CHAR(4) NOT NULL
)
```

## `date`

Create a column using a `DATE` equivalent type for your database.

| Argument |  Type  | Required | Default |       Description        |
|----------|--------|----------|---------|--------------------------|
| name     | string | `true`   |         | The name for the column. |

#### Example:

##### SchemaBuilder
```
schema.table( "users", function( table ) {
	table.date( "birthday" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `users` (
	`birthday` DATE NOT NULL
)
```

## `datetime`

Create a column using a `DATETIME` equivalent type for your database.

| Argument |  Type  | Required | Default |       Description        |
|----------|--------|----------|---------|--------------------------|
| name     | string | `true`   |         | The name for the column. |

#### Example:

##### SchemaBuilder
```
schema.table( "users", function( table ) {
	table.datetime( "hire_date" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `users` (
	`hire_date` DATETIME NOT NULL
)
```

## `decimal`

Create a column using a `DECIMAL` equivalent type for your database. The length and precision can be specified as the second and third arguments.

|  Argument |   Type  | Required | Default |         Description          |
|-----------|---------|----------|---------|------------------------------|
| name      | string  | `true`   |         | The name for the column.     |
| length    | numeric | `false`  |      10 | The length of the column.    |
| precision | numeric | `false`  |       0 | The precision of the column. |

#### Example (with defaults):

##### SchemaBuilder
```
schema.table( "weather", function( table ) {
	table.decimal( "temperature" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `weather` (
	`temperature` DECIMAL(10,0) NOT NULL
)
```

#### Example (with length):

##### SchemaBuilder
```
schema.table( "weather", function( table ) {
	table.decimal( "temperature", 4 );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `weather` (
	`temperature` DECIMAL(4,0) NOT NULL
)
```

#### Example (with precision):

##### SchemaBuilder
```
schema.table( "weather", function( table ) {
	table.decimal( name = "temperature", precision = 2 );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `weather` (
	`temperature` DECIMAL(10,2) NOT NULL
)
```

## `enum`

Create a column using a `ENUM` equivalent type for your database.

| Argument |  Type  | Required | Default |       Description        |
|----------|--------|----------|---------|--------------------------|
| name     | string | `true`   |         | The name for the column. |

#### Example:

##### SchemaBuilder
```
schema.table( "users", function( table ) {
	table.enum( "tshirt_size", [ "S", "M", "L", "XL", "XXL" ] );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `users` (
	`tshirt_size` ENUM(`S`, `M`, `L`, `XL`, `XXL`) NOT NULL
)
```

## `float`

Create a column using a `FLOAT` equivalent type for your database. The length and precision can be specified as the second and third arguments.

|  Argument |   Type  | Required | Default |         Description          |
|-----------|---------|----------|---------|------------------------------|
| name      | string  | `true`   |         | The name for the column.     |
| length    | numeric | `false`  |      10 | The length of the column.    |
| precision | numeric | `false`  |       0 | The precision of the column. |

#### Example (with defaults):

##### SchemaBuilder
```
schema.table( "weather", function( table ) {
	table.float( "temperature" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `weather` (
	`temperature` FLOAT(10,0) NOT NULL
)
```

#### Example (with length):

##### SchemaBuilder
```
schema.table( "weather", function( table ) {
	table.float( "temperature", 4 );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `weather` (
	`temperature` FLOAT(4,0) NOT NULL
)
```

#### Example (with precision):

##### SchemaBuilder
```
schema.table( "weather", function( table ) {
	table.float( name = "temperature", precision = 2 );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `weather` (
	`temperature` FLOAT(10,2) NOT NULL
)
```

## `increments`

Create an auto-incrementing column using an unsigned `INTEGER` type.  This column is also set as the primary key for the table.

|  Argument |  Type  | Required | Default |                                                                   Description                                                                   |
|-----------|--------|----------|---------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| name      | string | `true`   |         | The name for the column.                                                                                                                         |
| indexName | string | `false`  |         | The name for the primary key index.  If no name is passed in, the name will be dynamically created based off of the table name and column name. |

#### Example:

##### SchemaBuilder
```
schema.table( "users", function( table ) {
	table.increments( "id" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `users` (
	`id` INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
	CONSTRAINT `pk_users_id` PRIMARY KEY (`id`)
)
```

## `integer`

Create a column using a `INTEGER` equivalent type for your database.

|  Argument |   Type  | Required | Default |          Description          |
|-----------|---------|----------|---------|-------------------------------|
| name      | string  | `true`   |         | The name for the column.      |
| precision | numeric | `false`  |         | The precision for the column. |

#### Example (no precision):

##### SchemaBuilder
```
schema.table( "games", function( table ) {
	table.integer( "score" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `games` (
	`score` INTEGER NOT NULL
)
```

#### Example (with precision):

##### SchemaBuilder
```
schema.table( "games", function( table ) {
	table.integer( "score", 3 );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `games` (
	`score` INTEGER(3) NOT NULL
)
```

## `json`

Create a column using a `JSON` equivalent type for your database.

| Argument |  Type  | Required | Default |       Description        |
|----------|--------|----------|---------|--------------------------|
| name     | string | `true`   |         | The name for the column. |

#### Example:

##### SchemaBuilder
```
schema.table( "users", function( table ) {
	table.json( "options" ).nullable();
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `users` (
	`options` JSON
)
```

## `longText`

Create a column using a `LONGTEXT` equivalent type for your database.

| Argument |  Type  | Required | Default |       Description        |
|----------|--------|----------|---------|--------------------------|
| name     | string | `true`   |         | The name for the column. |

#### Example:

##### SchemaBuilder
```
schema.table( "posts", function( table ) {
	table.longText( "body" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `posts` (
	`body` LONGTEXT NOT NULL
)
```

## `mediumIncrements`

Create an auto-incrementing column using an unsigned `MEDIUMINT` type.  This column is also set as the primary key for the table.

|  Argument |  Type  | Required | Default |                                                                   Description                                                                   |
|-----------|--------|----------|---------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| name      | string | `true`   |         | The name for the column.                                                                                                                         |
| indexName | string | `false`  |         | The name for the primary key index.  If no name is passed in, the name will be dynamically created based off of the table name and column name. |

#### Example:

##### SchemaBuilder
```
schema.table( "users", function( table ) {
	table.mediumIncrements( "id" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `users` (
	`id` MEDIUMINT UNSIGNED NOT NULL AUTO_INCREMENT,
	CONSTRAINT `pk_users_id` PRIMARY KEY (`id`)
)
```

## `mediumInteger`

Create a column using a `MEDIUMINT` equivalent type for your database.

|  Argument |   Type  | Required | Default |          Description          |
|-----------|---------|----------|---------|-------------------------------|
| name      | string  | `true`   |         | The name for the column.      |
| precision | numeric | `false`  |      10 | The precision for the column. |

#### Example (no precision):

##### SchemaBuilder
```
schema.table( "games", function( table ) {
	table.mediumInteger( "score" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `games` (
	`score` MEDIUMINT NOT NULL
)
```

#### Example (with precision):

##### SchemaBuilder
```
schema.table( "games", function( table ) {
	table.mediumInteger( "score", 5 );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `games` (
	`score` MEDIUMINT(5) NOT NULL
)
```

## `mediumText`

Create a column using a `MEDIUMTEXT` equivalent type for your database.

| Argument |  Type  | Required | Default |       Description        |
|----------|--------|----------|---------|--------------------------|
| name     | string | `true`   |         | The name for the column. |

#### Example:

##### SchemaBuilder
```
schema.table( "posts", function( table ) {
	table.mediumText( "body" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `posts` (
	`body` MEDIUMTEXT NOT NULL
)
```

## `morphs`

Creates the necessary columns for a polymorphic relationship.  It takes the name provided and creates an `_id` and an `_type` column.

If you want different names for your polymorphic relationship columns, feel free to call other schema builder methods individually.

| Argument |  Type  | Required | Default |               Description               |
|----------|--------|----------|---------|-----------------------------------------|
| name     | string | `true`   |         | The prefix for the polymorphic columns. |

#### Example:

##### SchemaBuilder
```
schema.table( "tags", function( table ) {
	table.morphs( "taggable" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `tags` (
	`taggable_id` INTEGER UNSIGNED NOT NULL,
	`taggable_type` VARCHAR(255) NOT NULL,
	INDEX `taggable_index` (`taggable_id`, `taggable_type`)
)
```

## `nullableMorphs`

Creates the necessary columns for a polymorphic relationship.  It takes the name provided and creates an `_id` and an `_type` column. The only difference between this method and `morphs` is that the columns created here are nullable.

If you want different names for your polymorphic relationship columns, feel free to call other schema builder methods individually.

| Argument |  Type  | Required | Default |               Description               |
|----------|--------|----------|---------|-----------------------------------------|
| name     | string | `true`   |         | The prefix for the polymorphic columns. |

#### Example:

##### SchemaBuilder
```
schema.table( "tags", function( table ) {
	table.nullableMorphs( "taggable" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `tags` (
	`taggable_id` INTEGER UNSIGNED,
	`taggable_type` VARCHAR(255),
	INDEX `taggable_index` (`taggable_id`, `taggable_type`)
)
```

## `smallIncrements`

Create an auto-incrementing column using an unsigned `SMALLINT` type.  This column is also set as the primary key for the table.

|  Argument |  Type  | Required | Default |                                                                   Description                                                                   |
|-----------|--------|----------|---------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| name      | string | `true`   |         | The name for the column.                                                                                                                         |
| indexName | string | `false`  |         | The name for the primary key index.  If no name is passed in, the name will be dynamically created based off of the table name and column name. |

#### Example:

##### SchemaBuilder
```
schema.table( "users", function( table ) {
	table.smallIncrements( "id" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `users` (
	`id` SMALLINT UNSIGNED NOT NULL AUTO_INCREMENT,
	CONSTRAINT `pk_users_id` PRIMARY KEY (`id`)
)
```

## `smallInteger`

Create a column using a `SMALLINT` equivalent type for your database.

|  Argument |   Type  | Required | Default |          Description          |
|-----------|---------|----------|---------|-------------------------------|
| name      | string  | `true`   |         | The name for the column.      |
| precision | numeric | `false`  |         | The precision for the column. |

#### Example (no precision):

##### SchemaBuilder
```
schema.table( "games", function( table ) {
	table.smallInteger( "score" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `games` (
	`score` SMALLINT NOT NULL
)
```

#### Example (with precision):

##### SchemaBuilder
```
schema.table( "games", function( table ) {
	table.smallInteger( "score", 3 );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `games` (
	`score` SMALLINT(3) NOT NULL
)
```

## `string`

Create a column using a `VARCHAR` equivalent type for your database.

| Argument |   Type  | Required | Default |        Description        |
|----------|---------|----------|---------|---------------------------|
| name     | string  | `true`   |         | The name for the column.  |
| length   | numeric | `false`  |     255 | The length of the column. |

#### Example (with defaults):

##### SchemaBuilder
```
schema.table( "users", function( table ) {
	table.string( "username" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `users` (
	`username` VARCHAR(255) NOT NULL
)
```

#### Example (with length):

##### SchemaBuilder
```
schema.table( "users", function( table ) {
	table.string( "username", 50 );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `users` (
	`username` VARCHAR(50) NOT NULL
)
```

## `text`

Create a column using a `TEXT` equivalent type for your database.

| Argument |  Type  | Required | Default |       Description        |
|----------|--------|----------|---------|--------------------------|
| name     | string | `true`   |         | The name for the column. |

#### Example:

##### SchemaBuilder
```
schema.table( "posts", function( table ) {
	table.text( "body" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `posts` (
	`body` TEXT NOT NULL
)
```

## `time`

Create a column using a `TIME` equivalent type for your database.

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

#### Example:

##### SchemaBuilder
```
schema.table( "races", function( table ) {
	table.time( "finish_time" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `races` (
	`finish_time` TIME NOT NULL
)
```

## `timestamp`

Create a column using a `TIMESTAMP` equivalent type for your database.

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

#### Example:

##### SchemaBuilder
```
schema.table( "users", function( table ) {
	table.timestamp( "created_at" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `users` (
	`created_at` TIMESTAMP NOT NULL
)
```

## `tinyIncrements`

Create an auto-incrementing column using an unsigned `TINYINT` type.  This column is also set as the primary key for the table.

|  Argument |  Type  | Required | Default |                                                                   Description                                                                   |
|-----------|--------|----------|---------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| name      | string | `true`   |         | The name for the column.                                                                                                                         |
| indexName | string | `false`  |         | The name for the primary key index.  If no name is passed in, the name will be dynamically created based off of the table name and column name. |

#### Example:

##### SchemaBuilder
```
schema.table( "users", function( table ) {
	table.tinyIncrements( "id" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `users` (
	`id` TINYINT UNSIGNED NOT NULL AUTO_INCREMENT,
	CONSTRAINT `pk_users_id` PRIMARY KEY (`id`)
)
```

## `tinyInteger`

Create a column using a `TINYINT` equivalent type for your database.

|  Argument |   Type  | Required | Default |          Description          |
|-----------|---------|----------|---------|-------------------------------|
| name      | string  | `true`   |         | The name for the column.      |
| precision | numeric | `false`  |         | The precision for the column. |

#### Example (no precision):

##### SchemaBuilder
```
schema.table( "games", function( table ) {
	table.tinyInteger( "score" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `games` (
	`score` TINYINT NOT NULL
)
```

#### Example (with precision):

##### SchemaBuilder
```
schema.table( "games", function( table ) {
	table.tinyInteger( "score", 3 );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `games` (
	`score` TINYINT(3) NOT NULL
)
```

## `unsignedBigInteger`

Create a column using a `UNSIGNED BIGINT` equivalent type for your database.

|  Argument |   Type  | Required | Default |          Description          |
|-----------|---------|----------|---------|-------------------------------|
| name      | string  | `true`   |         | The name for the column.      |
| precision | numeric | `false`  |         | The precision for the column. |

#### Example (no precision):

##### SchemaBuilder
```
schema.table( "games", function( table ) {
	table.unsignedBigInteger( "score" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `games` (
	`score` BIGINT UNSIGNED NOT NULL
)
```

#### Example (with precision):

##### SchemaBuilder
```
schema.table( "games", function( table ) {
	table.unsignedBigInteger( "score", 3 );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `games` (
	`score` BIGINT(3) UNSIGNED NOT NULL
)
```

## `unsignedInteger`

Create a column using a `UNSIGNED INTEGER` equivalent type for your database.

|  Argument |   Type  | Required | Default |          Description          |
|-----------|---------|----------|---------|-------------------------------|
| name      | string  | `true`   |         | The name for the column.      |
| precision | numeric | `false`  |         | The precision for the column. |

#### Example (no precision):

##### SchemaBuilder
```
schema.table( "games", function( table ) {
	table.unsignedInteger( "score" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `games` (
	`score` INTEGER UNSIGNED NOT NULL
)
```

#### Example (with precision):

##### SchemaBuilder
```
schema.table( "games", function( table ) {
	table.unsignedInteger( "score", 3 );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `games` (
	`score` INTEGER(3) UNSIGNED NOT NULL
)
```

## `unsignedMediumInteger`

Create a column using a `UNSIGNED MEDIUMINT` equivalent type for your database.

|  Argument |   Type  | Required | Default |          Description          |
|-----------|---------|----------|---------|-------------------------------|
| name      | string  | `true`   |         | The name for the column.      |
| precision | numeric | `false`  |         | The precision for the column. |

#### Example (no precision):

##### SchemaBuilder
```
schema.table( "games", function( table ) {
	table.unsignedMediumInteger( "score" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `games` (
	`score` MEDIUMINT UNSIGNED NOT NULL
)
```

#### Example (with precision):

##### SchemaBuilder
```
schema.table( "games", function( table ) {
	table.unsignedMediumInteger( "score", 3 );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `games` (
	`score` MEDIUMINT(3) UNSIGNED NOT NULL
)
```

## `unsignedSmallInteger`

Create a column using a `UNSIGNED SMALLINT` equivalent type for your database.

|  Argument |   Type  | Required | Default |          Description          |
|-----------|---------|----------|---------|-------------------------------|
| name      | string  | `true`   |         | The name for the column.      |
| precision | numeric | `false`  |         | The precision for the column. |

#### Example (no precision):

##### SchemaBuilder
```
schema.table( "games", function( table ) {
	table.unsignedSmallInteger( "score" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `games` (
	`score` SMALLINT UNSIGNED NOT NULL
)
```

#### Example (with precision):

##### SchemaBuilder
```
schema.table( "games", function( table ) {
	table.unsignedSmallInteger( "score", 3 );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `games` (
	`score` SMALLINT(3) UNSIGNED NOT NULL
)
```

## `unsignedTinyInteger`

Create a column using a `UNSIGNED TINYINT` equivalent type for your database.

|  Argument |   Type  | Required | Default |          Description          |
|-----------|---------|----------|---------|-------------------------------|
| name      | string  | `true`   |         | The name for the column.      |
| precision | numeric | `false`  |         | The precision for the column. |

#### Example (no precision):

##### SchemaBuilder
```
schema.table( "games", function( table ) {
	table.unsignedTinyInteger( "score" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `games` (
	`score` TINYINT UNSIGNED NOT NULL
)
```

#### Example (with precision):

##### SchemaBuilder
```
schema.table( "games", function( table ) {
	table.unsignedTinyInteger( "score", 3 );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `games` (
	`score` TINYINT(3) UNSIGNED NOT NULL
)
```

## `uuid`

Create a column using a `CHAR` equivalent type for your database and a length of 35. Used in conjunction with the CFML `createUUID` method.


| Argument |  Type  | Required | Default |       Description        |
|----------|--------|----------|---------|--------------------------|
| name     | string | `true`   |         | The name for the column. |

#### Example:

##### SchemaBuilder
```
schema.table( "users", function( table ) {
	table.uuid( "id" ).primaryKey();
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `games` (
	`id` VARCHAR(35) NOT NULL,
	CONSTRAINT `pk_users_id` PRIMARY KEY (`id`)
)
```

