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

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

#### Example:

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

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
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

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
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

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
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

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
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
	`id` INTEGER(10) UNSIGNED NOT NULL AUTO_INCREMENT,
	CONSTRAINT `pk_users_id` PRIMARY KEY (`id`)
)
```

## `integer`

Create a column using a `INTEGER` equivalent type for your database.

|  Argument |   Type  | Required | Default |          Description          |
|-----------|---------|----------|---------|-------------------------------|
| name      | string  | `true`   |         | The name for the column.      |
| precision | numeric | `false`  |      10 | The precision for the column. |

#### Example (with defaults):

##### SchemaBuilder
```
schema.table( "games", function( table ) {
	table.integer( "score" );
} );
```

##### SQL (MySQL)
```sql
CREATE TABLE `games` (
	`score` INTEGER(10) NOT NULL
)
```

#### Example (with length):

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

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
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

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

( name ) {

## `mediumIncrements`

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

( name, indexName ) {

## `mediumInteger`

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

( name, precision = 10 ) {

## `mediumText`

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

( name ) {

## `morphs`

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

( name ) {

## `nullableMorphs`

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

( name ) {

## `smallIncrements`

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

( name, indexName ) {

## `smallInteger`

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

( name, precision = 10 ) {

## `string`

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

( name, length ) {

## `text`

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

( name ) {

## `time`

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

( name ) {

## `timestamp`

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

( name ) {

## `tinyIncrements`

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

( name, indexName ) {

## `tinyInteger`

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

( name, precision = 10 ) {

## `unsignedBigInteger`

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

( name ) {

## `unsignedInteger`

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

( name ) {

## `unsignedMediumInteger`

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

( name ) {

## `unsignedSmallInteger`

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

( name ) {

## `unsignedTinyInteger`

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

( name ) {

## `uuid`

| Argument |  Type  | Required | Default |       Description       |
|----------|--------|----------|---------|-------------------------|
| name     | string | `true`   |         | The name for the column. |

( name ) {

