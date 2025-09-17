---
icon: trash-check
---

# Dropping Tables and Views

Dropping tables straightforward in `qb`.

> For dropping columns or constraints, see [Alter](alter.md).

## drop

Drop a table from the database.

| Argument | Type    | Required | Default | Description                                  |
| -------- | ------- | -------- | ------- | -------------------------------------------- |
| table    | string  | `true`   |         | The name of the table to drop.               |
| options  | struct  | `false`  | `{}`    | Options to pass to `queryExecute`.           |
| execute  | boolean | `false`  | `true`  | Run the query immediately after building it. |

**Example:**

**SchemaBuilder**

```javascript
schema.drop( "user_logins" );
```

**SQL (MySQL)**

```sql
DROP TABLE `user_logins`
```

## dropIfExists

Drop a table from the database if it exists.

| Argument | Type    | Required | Default | Description                                  |
| -------- | ------- | -------- | ------- | -------------------------------------------- |
| table    | string  | `true`   |         | The name of the table to drop.               |
| options  | struct  | `false`  | `{}`    | Options to pass to `queryExecute`.           |
| execute  | boolean | `false`  | `true`  | Run the query immediately after building it. |

**Example:**

**SchemaBuilder**

```javascript
schema.dropIfExists( "user_logins" );
```

**SQL (MySQL)**

```sql
DROP TABLE IF EXISTS `user_logins`
```

## dropView

Drop a table from the database.

| Argument | Type    | Required | Default | Description                                  |
| -------- | ------- | -------- | ------- | -------------------------------------------- |
| view     | string  | `true`   |         | The name of the view to drop.                |
| options  | struct  | `false`  | `{}`    | Options to pass to `queryExecute`.           |
| execute  | boolean | `false`  | `true`  | Run the query immediately after building it. |

**Example:**

**SchemaBuilder**

```javascript
schema.view( "user_logins" );
```

**SQL (MySQL)**

```sql
DROP VIEW `user_logins`
```

## truncate

Truncates the data from a table.

| Argument | Type    | Required | Default | Description                                  |
| -------- | ------- | -------- | ------- | -------------------------------------------- |
| table    | string  | `true`   |         | The name of the table to truncate the data.  |
| options  | struct  | `false`  | `{}`    | Options to pass to `queryExecute`.           |
| execute  | boolean | `false`  | `true`  | Run the query immediately after building it. |

**Example:**

**SchemaBuilder**

```javascript
schema.truncate( "user_logins" );
```

**SQL (MySQL)**

```sql
TRUNCATE TABLE `user_logins`
```
