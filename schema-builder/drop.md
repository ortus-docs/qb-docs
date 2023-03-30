# Drop

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
