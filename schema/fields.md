# Fields

The `Blueprint` object has many field types available to construct your table schema.  Additionally, you can modify the columns created [with an additional set of methods](schema/modifiers.md) and [indexes](schema/indexes.md).

> The converted SQL below will be for the `MySQLGrammar`.

|                    fields                   |                                     |                                                 |                                               |
|---------------------------------------------|-------------------------------------|-------------------------------------------------|-----------------------------------------------|
| [bigIncrements](#bigIncrements)             | [bigInteger](#bigInteger)           | [bit](#bit)                                     | [boolean](#boolean)                           |
| [char](#char)                               | [date](#date)                       | [datetime](#datetime)                           | [decimal](#decimal)                           |
| [enum](#enum)                               | [float](#float)                     | [increments](#increments)                       | [integer](#integer)                           |
| [json](#json)                               | [longText](#longText)               | [mediumIncrements](#mediumIncrements)           | [mediumInteger](#mediumInteger)               |
| [mediumText](#mediumText)                   | [morphs](#morphs)                   | [nullableMorphs](#nullableMorphs)               | [raw](#raw)                                   |
| [smallIncrements](#smallIncrements)         | [smallInteger](#smallInteger)       | [string](#string)                               | [text](#text)                                 |
| [time](#time)                               | [timestamp](#timestamp)             | [tinyIncrements](#tinyIncrements)               | [tinyInteger](#tinyInteger)                   |
| [unsignedBigInteger](#unsignedBigInteger)   | [unsignedInteger](#unsignedInteger) | [unsignedMediumInteger](#unsignedMediumInteger) | [unsignedSmallInteger](#unsignedSmallInteger) |
| [unsignedTinyInteger](#unsignedTinyInteger) | [uuid](#uuid)                       |                                                 |                                               |


## bigIncrements

Create an auto-incrementing column using an unsigned `BIGINT` type.  This column is also set as the primary key for the table.

|  Argument |  Type  | Required | Default |                                                                   Description                                                                   |
|-----------|--------|----------|---------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| name      | string | `true`   |         | The name for the column.                                                                                                                         |
| indexName | string | `false`  |         | The name for the primary key index.  If no name is passed in, the name will be dynamically created based off of the table name and column name. |

**Example:**

__SchemaBuilder__
```
schema.create( "users", function( table ) {
	table.bigIncrements( "id" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `users` (
	`id` BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
	CONSTRAINT `pk_users_id` PRIMARY KEY (`id`)
)
```

## bigInteger

Create a column using a `BIGINT` equivalent type for your database.

|  Argument |   Type  | Required | Default |          Description          |
|-----------|---------|----------|---------|-------------------------------|
| name      | string  | `true`   |         | The name for the column.      |
| precision | numeric | `false`  |         | The precision for the column. |

**Example (no precision):**

__SchemaBuilder__
```
schema.create( "users", function( table ) {
	table.bigInteger( "salary" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `users` (
	`salary` BIGINT NOT NULL
)
```

**Example (with precision):**

__SchemaBuilder__
```
schema.create( "users", function( table ) {
	table.bigInteger( "salary", 5 );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `users` (
	`salary` BIGINT(5) NOT NULL
)
```

## bit

Create a column using a `BIT` equivalent type for your database.  The length can be specified as the second argument.

| Argument |   Type  | Required | Default |        Description         |
|----------|---------|----------|---------|----------------------------|
| name     | string  | `true`   |         | The name for the column.   |
| length   | numeric | `false`  |       1 | The length for the column. |

**Example (default length):**

__SchemaBuilder__
```
schema.create( "users", function( table ) {
	table.bit( "is_active" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `users` (
	`is_active` BIT(1) NOT NULL
)
```

**Example (custom length):**

__SchemaBuilder__
```
schema.create( "users", function( table ) {
	table.bit( "is_active", 2 );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `users` (
	`is_active` BIT(2) NOT NULL
)
```

## boolean

Create a column using a `BOOLEAN` equivalent type for your database.

| Argument |  Type  | Required | Default |       Description        |
|----------|--------|----------|---------|--------------------------|
| name     | string | `true`   |         | The name for the column. |

**Example:**

__SchemaBuilder__
```
schema.create( "users", function( table ) {
	table.boolean( "is_subscribed" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `users` (
	`is_subscribed` TINYINT(1) NOT NULL
)
```

## char

Create a column using a `CHAR` equivalent type for your database.

| Argument |   Type  | Required | Default |        Description         |
|----------|---------|----------|---------|----------------------------|
| name     | string  | `true`   |         | The name for the column.   |
| length   | numeric | `false`  |       1 | The length for the column. |

**Example (default length):**

__SchemaBuilder__
```
schema.create( "students", function( table ) {
	table.char( "grade" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `students` (
	`grade` CHAR(1) NOT NULL
)
```

**Example (custom length):**

__SchemaBuilder__
```
schema.create( "users", function( table ) {
	table.char( "tshirt_size", 4 );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `users` (
	`tshirt_size` CHAR(4) NOT NULL
)
```

## date

Create a column using a `DATE` equivalent type for your database.

| Argument |  Type  | Required | Default |       Description        |
|----------|--------|----------|---------|--------------------------|
| name     | string | `true`   |         | The name for the column. |

**Example:**

__SchemaBuilder__
```
schema.create( "users", function( table ) {
	table.date( "birthday" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `users` (
	`birthday` DATE NOT NULL
)
```

## datetime

Create a column using a `DATETIME` equivalent type for your database.

| Argument |  Type  | Required | Default |       Description        |
|----------|--------|----------|---------|--------------------------|
| name     | string | `true`   |         | The name for the column. |

**Example:**

__SchemaBuilder__
```
schema.create( "users", function( table ) {
	table.datetime( "hire_date" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `users` (
	`hire_date` DATETIME NOT NULL
)
```

## decimal

Create a column using a `DECIMAL` equivalent type for your database. The length and precision can be specified as the second and third arguments.

|  Argument |   Type  | Required | Default |         Description          |
|-----------|---------|----------|---------|------------------------------|
| name      | string  | `true`   |         | The name for the column.     |
| length    | numeric | `false`  |      10 | The length of the column.    |
| precision | numeric | `false`  |       0 | The precision of the column. |

**Example (with defaults):**

__SchemaBuilder__
```
schema.create( "weather", function( table ) {
	table.decimal( "temperature" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `weather` (
	`temperature` DECIMAL(10,0) NOT NULL
)
```

**Example (with length):**

__SchemaBuilder__
```
schema.create( "weather", function( table ) {
	table.decimal( "temperature", 4 );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `weather` (
	`temperature` DECIMAL(4,0) NOT NULL
)
```

**Example (with precision):**

__SchemaBuilder__
```
schema.create( "weather", function( table ) {
	table.decimal( name = "temperature", precision = 2 );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `weather` (
	`temperature` DECIMAL(10,2) NOT NULL
)
```

## enum

Create a column using a `ENUM` equivalent type for your database.

| Argument |  Type  | Required | Default |       Description        |
|----------|--------|----------|---------|--------------------------|
| name     | string | `true`   |         | The name for the column. |

**Example:**

__SchemaBuilder__
```
schema.create( "users", function( table ) {
	table.enum( "tshirt_size", [ "S", "M", "L", "XL", "XXL" ] );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `users` (
	`tshirt_size` ENUM(`S`, `M`, `L`, `XL`, `XXL`) NOT NULL
)
```

## float

Create a column using a `FLOAT` equivalent type for your database. The length and precision can be specified as the second and third arguments.

|  Argument |   Type  | Required | Default |         Description          |
|-----------|---------|----------|---------|------------------------------|
| name      | string  | `true`   |         | The name for the column.     |
| length    | numeric | `false`  |      10 | The length of the column.    |
| precision | numeric | `false`  |       0 | The precision of the column. |

**Example (with defaults):**

__SchemaBuilder__
```
schema.create( "weather", function( table ) {
	table.float( "temperature" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `weather` (
	`temperature` FLOAT(10,0) NOT NULL
)
```

**Example (with length):**

__SchemaBuilder__
```
schema.create( "weather", function( table ) {
	table.float( "temperature", 4 );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `weather` (
	`temperature` FLOAT(4,0) NOT NULL
)
```

**Example (with precision):**

__SchemaBuilder__
```
schema.create( "weather", function( table ) {
	table.float( name = "temperature", precision = 2 );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `weather` (
	`temperature` FLOAT(10,2) NOT NULL
)
```

## increments

Create an auto-incrementing column using an unsigned `INTEGER` type.  This column is also set as the primary key for the table.

|  Argument |  Type  | Required | Default |                                                                   Description                                                                   |
|-----------|--------|----------|---------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| name      | string | `true`   |         | The name for the column.                                                                                                                         |
| indexName | string | `false`  |         | The name for the primary key index.  If no name is passed in, the name will be dynamically created based off of the table name and column name. |

**Example:**

__SchemaBuilder__
```
schema.create( "users", function( table ) {
	table.increments( "id" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `users` (
	`id` INTEGER UNSIGNED NOT NULL AUTO_INCREMENT,
	CONSTRAINT `pk_users_id` PRIMARY KEY (`id`)
)
```

## integer

Create a column using a `INTEGER` equivalent type for your database.

|  Argument |   Type  | Required | Default |          Description          |
|-----------|---------|----------|---------|-------------------------------|
| name      | string  | `true`   |         | The name for the column.      |
| precision | numeric | `false`  |         | The precision for the column. |

**Example (no precision):**

__SchemaBuilder__
```
schema.create( "games", function( table ) {
	table.integer( "score" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `games` (
	`score` INTEGER NOT NULL
)
```

**Example (with precision):**

__SchemaBuilder__
```
schema.create( "games", function( table ) {
	table.integer( "score", 3 );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `games` (
	`score` INTEGER(3) NOT NULL
)
```

## json

Create a column using a `JSON` equivalent type for your database.

| Argument |  Type  | Required | Default |       Description        |
|----------|--------|----------|---------|--------------------------|
| name     | string | `true`   |         | The name for the column. |

**Example:**

__SchemaBuilder__
```
schema.create( "users", function( table ) {
	table.json( "options" ).nullable();
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `users` (
	`options` JSON
)
```

## longText

Create a column using a `LONGTEXT` equivalent type for your database.

| Argument |  Type  | Required | Default |       Description        |
|----------|--------|----------|---------|--------------------------|
| name     | string | `true`   |         | The name for the column. |

**Example:**

__SchemaBuilder__
```
schema.create( "posts", function( table ) {
	table.longText( "body" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `posts` (
	`body` LONGTEXT NOT NULL
)
```

## mediumIncrements

Create an auto-incrementing column using an unsigned `MEDIUMINT` type.  This column is also set as the primary key for the table.

|  Argument |  Type  | Required | Default |                                                                   Description                                                                   |
|-----------|--------|----------|---------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| name      | string | `true`   |         | The name for the column.                                                                                                                         |
| indexName | string | `false`  |         | The name for the primary key index.  If no name is passed in, the name will be dynamically created based off of the table name and column name. |

**Example:**

__SchemaBuilder__
```
schema.create( "users", function( table ) {
	table.mediumIncrements( "id" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `users` (
	`id` MEDIUMINT UNSIGNED NOT NULL AUTO_INCREMENT,
	CONSTRAINT `pk_users_id` PRIMARY KEY (`id`)
)
```

## mediumInteger

Create a column using a `MEDIUMINT` equivalent type for your database.

|  Argument |   Type  | Required | Default |          Description          |
|-----------|---------|----------|---------|-------------------------------|
| name      | string  | `true`   |         | The name for the column.      |
| precision | numeric | `false`  |      10 | The precision for the column. |

**Example (no precision):**

__SchemaBuilder__
```
schema.create( "games", function( table ) {
	table.mediumInteger( "score" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `games` (
	`score` MEDIUMINT NOT NULL
)
```

**Example (with precision):**

__SchemaBuilder__
```
schema.create( "games", function( table ) {
	table.mediumInteger( "score", 5 );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `games` (
	`score` MEDIUMINT(5) NOT NULL
)
```

## mediumText

Create a column using a `MEDIUMTEXT` equivalent type for your database.

| Argument |  Type  | Required | Default |       Description        |
|----------|--------|----------|---------|--------------------------|
| name     | string | `true`   |         | The name for the column. |

**Example:**

__SchemaBuilder__
```
schema.create( "posts", function( table ) {
	table.mediumText( "body" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `posts` (
	`body` MEDIUMTEXT NOT NULL
)
```

## morphs

Creates the necessary columns for a polymorphic relationship.  It takes the name provided and creates an `_id` and an `_type` column.

If you want different names for your polymorphic relationship columns, feel free to call other schema builder methods individually.

| Argument |  Type  | Required | Default |               Description               |
|----------|--------|----------|---------|-----------------------------------------|
| name     | string | `true`   |         | The prefix for the polymorphic columns. |

**Example:**

__SchemaBuilder__
```
schema.create( "tags", function( table ) {
	table.morphs( "taggable" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `tags` (
	`taggable_id` INTEGER UNSIGNED NOT NULL,
	`taggable_type` VARCHAR(255) NOT NULL,
	INDEX `taggable_index` (`taggable_id`, `taggable_type`)
)
```

## nullableMorphs

Creates the necessary columns for a polymorphic relationship.  It takes the name provided and creates an `_id` and an `_type` column. The only difference between this method and `morphs` is that the columns created here are nullable.

If you want different names for your polymorphic relationship columns, feel free to call other schema builder methods individually.

| Argument |  Type  | Required | Default |               Description               |
|----------|--------|----------|---------|-----------------------------------------|
| name     | string | `true`   |         | The prefix for the polymorphic columns. |

**Example:**

__SchemaBuilder__
```
schema.create( "tags", function( table ) {
	table.nullableMorphs( "taggable" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `tags` (
	`taggable_id` INTEGER UNSIGNED,
	`taggable_type` VARCHAR(255),
	INDEX `taggable_index` (`taggable_id`, `taggable_type`)
)
```

## raw

An escape hatch to directly insert any sql in to the statement.

| Argument |  Type  | Required | Default |                  Description                   |
|----------|--------|----------|---------|------------------------------------------------|
| sql      | string | `true`   |         | The sql to insert directly into the statement. |

**Example:**

__SchemaBuilder__
```
schema.create( "users", function( table ) {
	table.raw( "`profile_image` BLOB NOT NULL" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `users` (
	`profile_image` BLOB NOT NULL
)
```

## smallIncrements

Create an auto-incrementing column using an unsigned `SMALLINT` type.  This column is also set as the primary key for the table.

|  Argument |  Type  | Required | Default |                                                                   Description                                                                   |
|-----------|--------|----------|---------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| name      | string | `true`   |         | The name for the column.                                                                                                                         |
| indexName | string | `false`  |         | The name for the primary key index.  If no name is passed in, the name will be dynamically created based off of the table name and column name. |

**Example:**

__SchemaBuilder__
```
schema.create( "users", function( table ) {
	table.smallIncrements( "id" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `users` (
	`id` SMALLINT UNSIGNED NOT NULL AUTO_INCREMENT,
	CONSTRAINT `pk_users_id` PRIMARY KEY (`id`)
)
```

## smallInteger

Create a column using a `SMALLINT` equivalent type for your database.

|  Argument |   Type  | Required | Default |          Description          |
|-----------|---------|----------|---------|-------------------------------|
| name      | string  | `true`   |         | The name for the column.      |
| precision | numeric | `false`  |         | The precision for the column. |

**Example (no precision):**

__SchemaBuilder__
```
schema.create( "games", function( table ) {
	table.smallInteger( "score" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `games` (
	`score` SMALLINT NOT NULL
)
```

**Example (with precision):**

__SchemaBuilder__
```
schema.create( "games", function( table ) {
	table.smallInteger( "score", 3 );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `games` (
	`score` SMALLINT(3) NOT NULL
)
```

## string

Create a column using a `VARCHAR` equivalent type for your database.

| Argument |   Type  | Required | Default |        Description        |
|----------|---------|----------|---------|---------------------------|
| name     | string  | `true`   |         | The name for the column.  |
| length   | numeric | `false`  |     255 | The length of the column. |

**Example (with defaults):**

__SchemaBuilder__
```
schema.create( "users", function( table ) {
	table.string( "username" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `users` (
	`username` VARCHAR(255) NOT NULL
)
```

**Example (with length):**

__SchemaBuilder__
```
schema.create( "users", function( table ) {
	table.string( "username", 50 );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `users` (
	`username` VARCHAR(50) NOT NULL
)
```

## text

Create a column using a `TEXT` equivalent type for your database.

| Argument |  Type  | Required | Default |       Description        |
|----------|--------|----------|---------|--------------------------|
| name     | string | `true`   |         | The name for the column. |

**Example:**

__SchemaBuilder__
```
schema.create( "posts", function( table ) {
	table.text( "body" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `posts` (
	`body` TEXT NOT NULL
)
```

## time

Create a column using a `TIME` equivalent type for your database.

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

**Example:**

__SchemaBuilder__
```
schema.create( "races", function( table ) {
	table.time( "finish_time" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `races` (
	`finish_time` TIME NOT NULL
)
```

## timestamp

Create a column using a `TIMESTAMP` equivalent type for your database.

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

**Example:**

__SchemaBuilder__
```
schema.create( "users", function( table ) {
	table.timestamp( "created_at" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `users` (
	`created_at` TIMESTAMP NOT NULL
)
```

## tinyIncrements

Create an auto-incrementing column using an unsigned `TINYINT` type.  This column is also set as the primary key for the table.

|  Argument |  Type  | Required | Default |                                                                   Description                                                                   |
|-----------|--------|----------|---------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| name      | string | `true`   |         | The name for the column.                                                                                                                         |
| indexName | string | `false`  |         | The name for the primary key index.  If no name is passed in, the name will be dynamically created based off of the table name and column name. |

**Example:**

__SchemaBuilder__
```
schema.create( "users", function( table ) {
	table.tinyIncrements( "id" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `users` (
	`id` TINYINT UNSIGNED NOT NULL AUTO_INCREMENT,
	CONSTRAINT `pk_users_id` PRIMARY KEY (`id`)
)
```

## tinyInteger

Create a column using a `TINYINT` equivalent type for your database.

|  Argument |   Type  | Required | Default |          Description          |
|-----------|---------|----------|---------|-------------------------------|
| name      | string  | `true`   |         | The name for the column.      |
| precision | numeric | `false`  |         | The precision for the column. |

**Example (no precision):**

__SchemaBuilder__
```
schema.create( "games", function( table ) {
	table.tinyInteger( "score" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `games` (
	`score` TINYINT NOT NULL
)
```

**Example (with precision):**

__SchemaBuilder__
```
schema.create( "games", function( table ) {
	table.tinyInteger( "score", 3 );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `games` (
	`score` TINYINT(3) NOT NULL
)
```

## unsignedBigInteger

Create a column using a `UNSIGNED BIGINT` equivalent type for your database.

|  Argument |   Type  | Required | Default |          Description          |
|-----------|---------|----------|---------|-------------------------------|
| name      | string  | `true`   |         | The name for the column.      |
| precision | numeric | `false`  |         | The precision for the column. |

**Example (no precision):**

__SchemaBuilder__
```
schema.create( "games", function( table ) {
	table.unsignedBigInteger( "score" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `games` (
	`score` BIGINT UNSIGNED NOT NULL
)
```

**Example (with precision):**

__SchemaBuilder__
```
schema.create( "games", function( table ) {
	table.unsignedBigInteger( "score", 3 );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `games` (
	`score` BIGINT(3) UNSIGNED NOT NULL
)
```

## unsignedInteger

Create a column using a `UNSIGNED INTEGER` equivalent type for your database.

|  Argument |   Type  | Required | Default |          Description          |
|-----------|---------|----------|---------|-------------------------------|
| name      | string  | `true`   |         | The name for the column.      |
| precision | numeric | `false`  |         | The precision for the column. |

**Example (no precision):**

__SchemaBuilder__
```
schema.create( "games", function( table ) {
	table.unsignedInteger( "score" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `games` (
	`score` INTEGER UNSIGNED NOT NULL
)
```

**Example (with precision):**

__SchemaBuilder__
```
schema.create( "games", function( table ) {
	table.unsignedInteger( "score", 3 );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `games` (
	`score` INTEGER(3) UNSIGNED NOT NULL
)
```

## unsignedMediumInteger

Create a column using a `UNSIGNED MEDIUMINT` equivalent type for your database.

|  Argument |   Type  | Required | Default |          Description          |
|-----------|---------|----------|---------|-------------------------------|
| name      | string  | `true`   |         | The name for the column.      |
| precision | numeric | `false`  |         | The precision for the column. |

**Example (no precision):**

__SchemaBuilder__
```
schema.create( "games", function( table ) {
	table.unsignedMediumInteger( "score" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `games` (
	`score` MEDIUMINT UNSIGNED NOT NULL
)
```

**Example (with precision):**

__SchemaBuilder__
```
schema.create( "games", function( table ) {
	table.unsignedMediumInteger( "score", 3 );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `games` (
	`score` MEDIUMINT(3) UNSIGNED NOT NULL
)
```

## unsignedSmallInteger

Create a column using a `UNSIGNED SMALLINT` equivalent type for your database.

|  Argument |   Type  | Required | Default |          Description          |
|-----------|---------|----------|---------|-------------------------------|
| name      | string  | `true`   |         | The name for the column.      |
| precision | numeric | `false`  |         | The precision for the column. |

**Example (no precision):**

__SchemaBuilder__
```
schema.create( "games", function( table ) {
	table.unsignedSmallInteger( "score" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `games` (
	`score` SMALLINT UNSIGNED NOT NULL
)
```

**Example (with precision):**

__SchemaBuilder__
```
schema.create( "games", function( table ) {
	table.unsignedSmallInteger( "score", 3 );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `games` (
	`score` SMALLINT(3) UNSIGNED NOT NULL
)
```

## unsignedTinyInteger

Create a column using a `UNSIGNED TINYINT` equivalent type for your database.

|  Argument |   Type  | Required | Default |          Description          |
|-----------|---------|----------|---------|-------------------------------|
| name      | string  | `true`   |         | The name for the column.      |
| precision | numeric | `false`  |         | The precision for the column. |

**Example (no precision):**

__SchemaBuilder__
```
schema.create( "games", function( table ) {
	table.unsignedTinyInteger( "score" );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `games` (
	`score` TINYINT UNSIGNED NOT NULL
)
```

**Example (with precision):**

__SchemaBuilder__
```
schema.create( "games", function( table ) {
	table.unsignedTinyInteger( "score", 3 );
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `games` (
	`score` TINYINT(3) UNSIGNED NOT NULL
)
```

## uuid

Create a column using a `CHAR` equivalent type for your database and a length of 35. Used in conjunction with the CFML `createUUID` method.


| Argument |  Type  | Required | Default |       Description        |
|----------|--------|----------|---------|--------------------------|
| name     | string | `true`   |         | The name for the column. |

**Example:**

__SchemaBuilder__
```
schema.create( "users", function( table ) {
	table.uuid( "id" ).primaryKey();
} );
```

__SQL (MySQL)__
```sql
CREATE TABLE `games` (
	`id` VARCHAR(35) NOT NULL,
	CONSTRAINT `pk_users_id` PRIMARY KEY (`id`)
)
```

