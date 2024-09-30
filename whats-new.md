# What's New?

## 10.0.0

* Full compatibility for running on [BoxLang](https://boxlang.io/) with the [`bx-compat-cfml`](https://forgebox.io/view/bx-compat-cfml) module.
* Internal property name changes for BoxLang compatibility as well as cleaner code. (This _may_ cause breaking changes, in rare cases.  See the [Migration Guide](migration-guide.md#v10.0.0) for more details.)

## 9.8.1

* Fix missing `parseNumber` function for ACF
* Add alias to `clone()`

## 9.8.0

Support alias renaming using [`withAlias`](query-builder/building-queries/from.md#withalias)&#x20;

## 9.7.1

Add in missing join compilations.

## 9.7.0

Implement [crossApply](query-builder/building-queries/joins.md#crossapply) and [outerApply](query-builder/building-queries/joins.md#outerapply) for supported Grammars

## 9.6.1

Expand type annotation for `from`. This can be a string or an Expression.

## 9.6.0

Make [`addBindings`](query-builder/building-queries/parameters-and-bindings.md#addbindings) and [`addBindingsFromBuilder`](query-builder/building-queries/parameters-and-bindings.md#addbindingsfrombuilder) publicly accessible.

## 9.5.1

Add MariaDB support to `AutoDiscover@qb` grammar. (It will choose the `MySQLGrammar@qb`.)

## 9.5.0

Add [`findOrFail`](query-builder/executing-queries/retrieving-results.md#findorfail) and [`existsOrFail`](query-builder/executing-queries/aggregates.md#existsorfail) methods, inspired by [Quick](https://quick.ortusbooks.com).

## 9.4.1

Better Support for OracleGrammar in SchemaBuilder

* Fix trigger creation by escaping colons (`:`).
* Try to drop associated sequences and triggers when dropping a table.
* Better support for creating a table in a different schema by only checking for the last identifier as the table name in [`hasTable`](schema-builder/schema-builder.md#hastable) and [`hasColumn`](schema-builder/schema-builder.md#hascolumn).

## 9.4.0

Allow for setting a [`defaultSchema`](schema-builder/schema-builder.md) property on a `SchemaBuilder` instance.

The `defaultSchema` will be used for methods like [`hasTable`](schema-builder/schema-builder.md#hastable) and [`hasColumn`](schema-builder/schema-builder.md#hascolumn). A passed in `schema` will still take precedence.

## 9.3.1

* Use `CHAR` for `GUID` and `UUID` types in MySQL.
* Don't call `getUtils` from inside `QueryUtils`.

## 9.3.0

Make [`replaceBindings`](query-builder/options-and-utilities/query-options.md#replacing-or-inlining-bindings) publicly available in `QueryUtils`.

This is used by qb to inline query bindings in `toSQL` or `dump` calls and can be used to inline the bindings in other tools like[ CommandBox Migrations](https://forgebox.io/view/commandbox-migrations).

## 9.2.5

Use named parameters when passing to `BaseGrammar`. This avoids problems where custom Grammars have extra arguments and we add arguments to the official grammar.

## 9.2.4

{% hint style="info" %}
We apologize for the new features in a patch release.
{% endhint %}

#### New Features

* Add the ability to pretend to run queries, both in [QueryBuilder](query-builder/debugging/#pretend) and [SchemaBuilder](schema-builder/debugging.md#pretend).
* Add query logging to [QueryBuilder](query-builder/debugging/#querylog) and [SchemaBuilder](schema-builder/debugging.md#querylog) instances.

#### Bug Fixes

* Use varchar for clob when converting to a CFML query. This is used when removing a column like in Oracle pagination.

## 9.2.3

Handle more numeric SQL types like `AtomicInteger` and `Long`.

## 9.2.2

Add millisecond accuracy to inline bindings.

## 9.2.1

Separate `having` bindings from `where` bindings.

## 9.2.0

### New Features

We now support the `returning` function inside `update` and `delete` statements for supported Grammars.  Supported grammars are SQL Server, Postgres, and SQLite.

### Bug Fixes

* Fix raw table name parsing in update queries for `SqlServerGrammar`.
* Fix truncating text in nested wheres inside joins.
* Fix out of order bindings in joinSub

## 9.1.5

Switch from `table_catalog` to `table_schema` when referencing schema for `PostgresGrammar`.

## 9.1.4

CommandBox-friendly injections for SQL Commenter.

## 9.1.3

Add support for `from` bindings, used especially in `fromSub` queries.

## 9.1.2

This release reverts the use of native `returntype`s.  There are too many bugs between engine implementations to make it viable.  No end-user changes should be visible.

## 9.1.1

Make `withReturnFormat` a public method.

## 9.1.0

### New Features

Add ability to inline bindings when calling `toSQL` and `dump`. These strings can be executed in a DBMS application.

### Bug Fixes

* Move `coldbox` namespace injection to the function body so CommandBox doesn't blow up.
* Correctly apply native returntypes after `newQuery` and `withReturnFormat`.

## 9.0.2

* Fix losing `defaultOptions` when calling `newQuery`.
* Shortcut for no return format using `none`.
* Allow for native struct returntypes. Requires a return format of `none`.

## 9.0.1

Fix `RouteInfoCommenter` file name.

## 9.0.0

### Breaking Changes

#### Dropped support for Adobe ColdFusion 2016

Adobe has ended support for ACF 2016, and so must we.

#### SchemaBuilder's `uuid` split into [guid()](schema-builder/columns.md#guid) and [uuid()](schema-builder/columns.md#uuid)

CFML's `uuid` does not match other languages; it's one character shorter. Because of this, the value from `createUUID()` cannot be used in some database column types like SQL Server's `uniqueidentifier`. This made for some confusion in SchemaBuilder since it wasn't clear if `uuid` meant CFML's definition or the wider world's definition.

So, the types have been split, following Lucee's pattern, into [`uuid`](schema-builder/columns.md#uuid) (matching CFML's [`createUUID()`](https://cfdocs.org/createuuid)) and [`guid`](schema-builder/columns.md#guid) (matching Java's UUID or [`createGUID()`](https://cfdocs.org/createguid) on Lucee).

#### Returning all rows from [paginate](query-builder/executing-queries/retrieving-results.md#paginate) when maxRows is  0 or lower

Popular grid frameworks like Quasar and Datatables use values of 0 or -1 to return all rows from a query. This is now supported in qb. Previously, it generated an invalid query (`SELECT * FROM users LIMIT 0 OFFSET 0`).

This behavior can be customized by providing a callback to the `shouldMaxRowsOverrideToAll` setting or `init` argument. For instance, to revert to the previous behavior you would set the function as follows:

```cfscript
moduleSettings = {
    "qb": {
        "shouldMaxRowsOverrideToAll": function( maxRows ) {
            return false;
        }
    }
};
```

#### [`autoDeriveNumericType`](query-builder/building-queries/parameters-and-bindings.md#numeric-sql-type) is now the default

Introduced in [8.10.0](whats-new.md#8.10.0), this feature uses separate SQL types for integers and decimals to increase performance in certain database grammars.  This feature is now the default, but the previous behavior can be enabled by setting `autoDeriveNumericType` to `false`.

{% hint style="warning" %}
**Note:** the option to revert to the old behavior will be removed in the next major version.
{% endhint %}

#### [`strictDateDetection`](query-builder/building-queries/parameters-and-bindings.md#strict-date-detection) is now the default

Introduced in [8.1.0](whats-new.md#8.1.0), this feature only returns a SQL type of `CF_SQL_TIMESTAMP` if the param is a date object, not just a string that looks like a date.  This helps avoid situations where some strings were incorrectly interpreted as dates.  For many, the migration path is straightforward — calls to [`now()`](https://cfdocs.org/now) are already date objects as well as any function that operates on a date.  If you need to parse a string as a date, the [`parseDateTime`](https://cfdocs.org/parsedatetime) built-in function can accomplish that.

{% hint style="warning" %}
**Note:** the option to revert to the old behavior **may** be removed in the next major version.
{% endhint %}

### New Features and Improvements

#### SQLite Grammar Support

Thanks to [Jason Steinhouer](https://github.com/jsteinshouer), qb now supports SQLite for both `QueryBuilder` and `SchemaBuilder`.  You can use it in your apps by specifying `SQLiteGrammar@qb` as the default grammar.

#### [sqlCommenter Support](query-builder/debugging/sqlcommenter.md)

sqlCommenter is a [specification by Google](https://google.github.io/sqlcommenter/) for adding contextual information as a comment at the end of a SQL statement.  This can give insights into your application, especially when diagnosing slow queries. Examples of the information you can append to your queries are `route`, `handler`, `action`, `version`, and others, as well as the ability to add your own, such as `loggedInUser` and more.

#### [sumRaw](query-builder/executing-queries/aggregates.md#sumraw) helper function

There's a new shortcut method to return `qb.sum( qb.raw( expression ) )`. You're welcome. 😉

#### Dedicated [`dropIndex`](schema-builder/alter.md#dropindex) method

Some grammars, like SQL Server, do not treat simple indexes as constraints.  For this reason, we've added a [`dropIndex`](schema-builder/alter.md#dropindex) method alongside the existing [`dropConstraint`](schema-builder/alter.md#dropconstraint).

#### [`columnList`](query-builder/executing-queries/aggregates.md#columnlist) helper method

[`columnList`](query-builder/executing-queries/aggregates.md#columnlist) will return either an array of column names for the configured table or the query that is generated by `cfdbinfo` for the configured table.  Especially useful when working with dynamically generated grids.&#x20;

### Bug Fixes

* Correctly compile `insertUsing` statements that use Common Table Expressions (CTEs).
* Update `announceInterception` calls for ColdBox 7. (Thank you, Michael Born.)
* Fixed `insertUsing` not placing Common Table Expressions (CTEs) in the correct order.
* Added the missing keyword in the Postgres [`upsert`](query-builder/executing-queries/inserts-updates-deletes.md#upsert) syntax.
* Don't add `DISTINCT` when doing a `COUNT(*)`.
* Support aggregates for unioned queries.

## 8.10.0

* Add a [`firstOrFail`](query-builder/executing-queries/retrieving-results.md#firstorfail) fetch method inspired by [Quick](https://quick.ortusbooks.com).
* There are now [specific numeric SQL types for integers and decimals](query-builder/building-queries/parameters-and-bindings.md#numeric-sql-type) used during the `inferSQLType` check in `QueryUtils`.  This is an opt-in feature, enabled by setting the `autoDeriveNumericType` setting. The previous approach was to use `CF_SQL_NUMERIC` for all numeric types which could cause performance issues in some grammars as they interpreted all `CF_SQL_NUMERIC` as floating point numbers.

## 8.9.1

* `HOLDLOCK` and `READPAST` are mutually exclusive table locks in SQL Server but were mistakenly being applied together.

## 8.9.0

* Specify `defaultOptions` [inside of your ColdBox config.](query-builder/options-and-utilities/query-options.md)

## 8.8.1

* Better parsing of `raw` statements when deriving [`insertUsing`](query-builder/executing-queries/inserts-updates-deletes.md#insertusing) columns.

## 8.8.0

### New Features and Improvements

* Insert data based off of a callback or builder using [`insertUsing`](query-builder/executing-queries/inserts-updates-deletes.md#insertusing).
* Insert data ignoring duplicate key errors using [`insertIgnore`](query-builder/executing-queries/inserts-updates-deletes.md#insertignore).
* Use a callback or builder as the source for an [`upsert`](query-builder/executing-queries/inserts-updates-deletes.md#upsert) statement.
* Allow for deleting unmatched source records in [upserts](query-builder/executing-queries/inserts-updates-deletes.md#upsert) (SQL Server only).
* Add a new `skipLocked` flag to [`lockForUpdate`](query-builder/building-queries/locks.md#lockforupdate).

### Bug Fixes

* Don't uppercase quoted aliases in Oracle.
* Fix for aliases in update statements.
* Don't sort columns for `insertUsing`.
* Add subquery bindings in insert and upsert statements.
* Maintain column order when using source in upsert.



## 8.7.8

* Fix for Oracle returning custom column types when renaming a column.

## 8.7.7

* Explicit arguments scoping.

## 8.7.6

* `arrayEach` is slow compared to merging arrays.

## 8.7.5

* Fix wheres with joins in update statements.

## 8.7.2

* Add better null handling to `inferSqlType`.

## 8.7.1

* Correctly format columns being updated.

## 8.7.0

### New Features and Improvements

* Add an [upsert](query-builder/executing-queries/inserts-updates-deletes.md#upsert) method.  `upsert` can update or insert multiple records at once depending on if a column is matched.
* Allow expressions in [`value`](query-builder/executing-queries/retrieving-results.md#value) and [`values`](query-builder/executing-queries/retrieving-results.md#values).  Also add a [`valueRaw`](query-builder/executing-queries/retrieving-results.md#valueraw) and [`valuesRaw`](query-builder/executing-queries/retrieving-results.md#valuesraw) helper method to make that pattern more ergonomic.
* Allow [`JOIN` statements in `UPDATE` statements](query-builder/executing-queries/inserts-updates-deletes.md#updating-with-joins).  (This is **not** supported on Oracle.)
* Allow [updates with subselects](query-builder/executing-queries/inserts-updates-deletes.md#updating-with-subselects) using closures or builder instances.

### Bug Fixes

* Better handling of [`group by`](query-builder/building-queries/group-by-and-having.md#groupby) and [`having`](query-builder/building-queries/group-by-and-having.md#having) clauses in [pagination](query-builder/building-queries/limit-offset-and-pagination.md#simplepaginate-and-paginate) queries.
* Allow any value to be returned from [aggregates](query-builder/executing-queries/aggregates.md) including strings, numbers, and dates.
* Provide default values for [sum](query-builder/executing-queries/aggregates.md#sum) and [count](query-builder/executing-queries/aggregates.md#count) if no records are returned.
* Test in CI with [full](https://coldfusion.adobe.com/2018/07/null-support-in-coldfusion-2018/) [null](https://docs.lucee.org/guides/cookbooks/NullSupport.html) support.

## 8.6.1

* Correctly wrap CTE expressions with parenthesis when required in certain grammars.

## 8.6.0

* `SchemaBuilder` can now be configured with [default query options](schema-builder/schema-builder.md).  (Default options will still be overridden by options passed to each `SchemaBuilder` method.)

## 8.5.0

### QueryBuilder

* Add a [`reset`](query-builder/options-and-utilities/clone-and-reset.md#reset) method to QueryBuilder.
* Add [locking](query-builder/building-queries/locks.md) helpers such as [`lock`](query-builder/building-queries/locks.md#lock), [`noLock`](query-builder/building-queries/locks.md#nolock), [`lockForUpdate`](query-builder/building-queries/locks.md#lockforupdate), and [`sharedLock`](query-builder/building-queries/locks.md#sharedlock).
* Correct return aggregate values for date values from `max` and `min` executors.
* [Automatically add a `scale`](query-builder/building-queries/parameters-and-bindings.md#automatic-scale-detection) to an incoming query param when needed.
* Add a [`whereNotLike`](query-builder/building-queries/wheres.md#wherenotlike) shortcut method.
* Correctly format a `COUNT(DISTINCT column)` query.
* Only use bulk insert syntax when needed in OracleGrammar due to interactions between the `result` parameter to `cfquery`, Lucee, and the Oracle JDBC driver.

### SchemaBuilder

* Add support for [stored computed columns](schema-builder/column-modifiers.md#storedas) and [virtual computed columns](schema-builder/column-modifiers.md#virtualas).

## 8.4.9

* Swap `master` branch to `main` branch.

## 8.4.8

* Remove unnecessary injection for QueryUtils.

## 8.4.7

* Account for raw expressions when generating mementos for comparison

## 8.4.6

* Add support for [mediumtext](schema-builder/columns.md#mediumtext) & [longtext](schema-builder/columns.md#longtext) types for MySQLGrammar.

## 8.4.5

* Fix limit on [simplePaginate](query-builder/executing-queries/retrieving-results.md#simplepaginate).

## 8.4.1 - 8.4.4

* Migrate release process to GitHub Actions.

## 8.4.0

* Add a `simplePaginate` pagination method for quicker performance when total records or total pages are not needed or too slow.

## 8.3.0

* Introduce a [`numericSQLType`](query-builder/building-queries/parameters-and-bindings.md#numeric-sql-type) setting to specify the default numeric SQL type.

## 8.2.2

* Default to `html` for the `dump` format argument to `writeDump`.

## 8.2.1

* Correctly use the passed in `strictDateDetection` to the `QueryUtils.cfc`.

## 8.2.0

{% hint style="success" %}
📹 [Watch a walkthrough of this change on CFCasts.](https://cfcasts.com/series/whats-new-in-qb-8)
{% endhint %}

* Added a [`dump`](query-builder/debugging/#dump) command to aid in debugging a query while chaining.

## 8.1.0

{% hint style="success" %}
📹 [Watch a walkthrough of these changes on CFCasts.](https://cfcasts.com/series/whats-new-in-qb-8)
{% endhint %}

* [`orderByRaw`](query-builder/building-queries/ordering-grouping-and-limit.md#order-by-raw) now can accept bindings.
* A new, optional [`strictDateDetection`](query-builder/building-queries/parameters-and-bindings.md#strict-date-detection) setting is available to check the underlying Java class of a date object instead of using `isDate`.

## 8.0.3

* Ignore select bindings for aggregate queries.
* Allow spaces in table aliases.
* Split FLOAT and DECIMAL column types in SQL Server.

## 8.0.2

* Clear orderBy bindings when calling `clearOrders`.

## 8.0.1

* Trim table definitions before searching for aliases.  Makes qb more lenient with extra whitespace.

## 8.0.0

{% hint style="success" %}
📹 [Watch a walkthrough of these changes on CFCasts.](https://cfcasts.com/series/whats-new-in-qb-8)
{% endhint %}

#### BREAKING CHANGES

* \`\`[`when`](query-builder/building-queries/when.md#when) callbacks now automatically scope and group where clauses when an `OR` combinator is used.

#### Other Changes

* Combine [`clearOrders`](query-builder/building-queries/ordering-grouping-and-limit.md#clearorders) and `orderBy` with a new [`reorder`](query-builder/building-queries/ordering-grouping-and-limit.md#reorder)method.
* Clear current selected columns with [`clearSelect`](query-builder/building-queries/selects.md#clearselect).
* Combine [`clearSelect`](query-builder/building-queries/selects.md#clearselect) and either [`select`](query-builder/building-queries/selects.md#get) or [`selectRaw`](query-builder/building-queries/selects.md#get-3) with [`reselect`](query-builder/building-queries/selects.md#reselect) and [`reselectRaw`](query-builder/building-queries/selects.md#reselectraw) respectively.

## 7.10.0

* Expose nested where functions to enable advanced query manipulation in downstream libraries like Quick.

## 7.9.9

* Fixes for OracleGrammar including table aliases and wrapped subqueries.

## 7.9.8

* Allow nullable [timestamps](schema-builder/columns.md#timestamp) in MySQL.

## 7.9.7

* Return 0 on null [aggregates](query-builder/executing-queries/aggregates.md).

## 7.9.6

* Match type hints to documentation for [join](query-builder/building-queries/joins.md) functions

## 7.9.5

* Handle enhanced numeric checks with Secure Profile enabled.

## 7.9.4

* Allow raw statements in basic where clauses.

## 7.9.3

* Passed along the options struct to the [`count`](query-builder/executing-queries/aggregates.md#count) method when calling [`paginate`](query-builder/building-queries/limit-offset-and-pagination.md#paginate).

## 7.9.2

* Allow for space-delimited [sort](query-builder/building-queries/ordering-grouping-and-limit.md) directions like `column DESC`.
* Add helpful message when trying to use a closure with [`from`](query-builder/building-queries/from.md#get) instead of [`fromSub`](query-builder/building-queries/from.md#get-3).
* \`\`[`value`](query-builder/executing-queries/retrieving-results.md#value) and [`values`](query-builder/executing-queries/retrieving-results.md#values) now work with [column formatters.](query-builder/options-and-utilities/column-formatter.md)
* Correctly format RETURNING clauses with [column formatters](query-builder/options-and-utilities/column-formatter.md) and ignoring table qualifiers.

## 7.9.1

* Handle multi-word columns in `queryRemoveColumns`.

## 7.9.0

* Remove elvis operator due to ACF compatibility issues

## 7.8.0

* Add support for [MONEY](schema-builder/columns.md#money) and [SMALLMONEY](schema-builder/columns.md#smallmoney) data types to [SchemaBuilder](schema-builder/create.md).

## 7.7.3

* Fix wrapping of [enum](schema-builder/columns.md#enum) types for Postgres.

## 7.7.2

* Compatibility fix for ACF 2018 and `listLast` parsing.
* Include current\_timestamp default for [`timestamp`](schema-builder/columns.md#timestamp) columns in SchemaBuilder.
* Ignore table qualifiers for insert and update.

## 7.7.1

* Fix a bug with preventDuplicateJoins when using the closure syntax with a join.

## 7.7.0

* Add executionTime to the data output from BaseGrammar, including being available in interceptors.

## 7.6.2

* Fix a case where a column was not wrapped correctly when a `where` used a subquery for the value.

## 7.6.1

* Avoid `duplicate` function due to cbORM / Hibernate bugs when used in the same application.

## 7.6.0

* Split off a private `whereBasic` method.  This is used in Quick to provide extra sql type features.
* Add a [`clearOrders`](query-builder/building-queries/ordering-grouping-and-limit.md#clearorders) method.  Any already configured orders are cleared.  Any orders added after this call will be added as normal.
* [`selectRaw`](query-builder/building-queries/selects.md#get-3) now can take an array of expressions.

## 7.5.1

Fixed an issue using column formatters with `update` and `insert`.

## 7.5.0

Using a new `preventDuplicateJoins` setting in the module settings, qb can detect duplicate joins and ignore them. This is especially useful in a heavily filtered and dynamic query where you may or may not need the join at all or more than one column may need the same join. `preventDuplicateJoins` defaults to `false`, so it is opt-in. It may be turned on by default in a future breaking release of qb.

## 7.4.0

Enhance order by's with more direction options ([c767ac8](https://github.com/coldbox-modules/qb/commit/c767ac8764fab70d70dc77baa7bb9fb27c1d4eeb))

You can now use two shortcut methods: `orderByAsc` and `orderByDesc`. Additionally, `orderBySub` or using `orderBy` with a closure or builder instance will respect the direction argument.

## 7.3.15

* Fix using `whereBetween` with query param structs ([07c9b72](https://github.com/coldbox-modules/qb/commit/07c9b728bdbad6bf02ccd9d21dbdf6968062c02e))

## 7.3.14

* Ignore orders in aggregate queries ([39e1338](https://github.com/coldbox-modules/qb/commit/39e1338a147838165e05225bd91ef7e6cde2319a))

## 7.3.13

* Format with cfformat ([dc2a9b6](https://github.com/coldbox-modules/qb/commit/dc2a9b61503690d753a71c3b7bce002ebdf4ccda))

## 7.3.12

* Improve column wrapping with trimming ([d98a5cb](https://github.com/coldbox-modules/qb/commit/d98a5cb65851c154b6755e90254d1a2c1df82833))
* Prefer the parent query over magic methods when the parent query has the exact method. ([f9fd8d1](https://github.com/coldbox-modules/qb/commit/f9fd8d157cdc0d7480811c4659c130ee1d58888f))

## 7.3.9, 7.3.10, 7.3.11

* Switch to using [ForgeBox Storage](https://commandbox.ortusbooks.com/forgebox-enterprise/storage#storing-package-binaries-on-forgebox).

## 7.3.8

* Allow passing query options in to paginate ([cdecfb3](https://github.com/coldbox-modules/qb/commit/cdecfb36f5acab87edd3a478c570f77d285df554))

## 7.3.7

* Fix for inserting null values directly ([1de27a6](https://github.com/coldbox-modules/qb/commit/1de27a697f65bfdeed63442ad66be47cd0d30344))

## 7.3.5, 7.3.6

* Use cfformat for automatic formatting ([119e434](https://github.com/coldbox-modules/qb/commit/119e434b307a2cc2323b857a214c20842cafbbd4))
* Add a type to the onMissingMethod exception ([90d1093](https://github.com/coldbox-modules/qb/commit/90d109312b2ea86c00db34020b12b5ab22bb377b))

## 7.3.4

* Correctly wrap [comments](schema-builder/column-modifiers.md#comment) in `MySQLGrammar`.

## 7.3.2, 7.3.3

* Publish qb apidocs to [Ortus API Docs](https://apidocs.ortussolutions.com/#/coldbox-modules/qb/).

## 7.3.1

* Fix for null values breaking the new `checkIsActuallyNumeric` method in `QueryUtils`.

## 7.3.0

* Add a `parameterLimit` public property to `SqlServerGrammar`.  This property is used in Quick to split up eager loading to work around the 2100 param limit of SQL Server.

## 7.2.0

* Allow a [parent query](broken-reference) to be set.  A parent query will receive any method calls that are not found on the Query Builder instance.  This is especially useful for instances like [Quick](https://quick.ortusbooks.com/) to allow Quick features like scopes to be available inside any closures.

## 7.1.0

* Lambdas (arrow functions) are now allowed wherever closures are allowed.
* Add an [`orderByRaw`](query-builder/building-queries/ordering-grouping-and-limit.md#order-by-raw) method.
* Allow for fully-qualified column names (`table_name.column.name`) in the [`value`](query-builder/executing-queries/retrieving-results.md#value) and [`values`](query-builder/executing-queries/retrieving-results.md#values) methods.

## 7.0.0

#### **BREAKING CHANGES**

_Please see the_ [_Migration Guide_](migration-guide.md#v-7-0-0) _for more information on these changes._

* Drop support for Lucee 4.5 and Adobe ColdFusion 11.
* `MSSQLGrammar` renamed to `SqlServerGrammar`
* Remove variadic parameters support in builder functions like `select`.
* The `defaultGrammar` mapping needs to be the full WireBox mapping, including the `@qb`, if needed.
  * For instance, `MSSQLGrammar` would become `MSSQLGrammar@qb`.
  * This will allow for other grammars to be more easily contributed via third party modules.
* The argument names of `forPage` changed to match the new `paginate` method.
* Add `defaultValue` and optional exception throwing to `value`. (This changed the argument order.)
* All methods that could conceivably take a subquery as well as a value now accept a closure or another builder instance to use as a subquery. (This changed the argument names in some instances.)

#### **Other Changes**

* Completely revamped documentation! (You're looking at it right now.)
* Add new flag to [`toSQL( showBindings = true )`](query-builder/debugging/#tosql) to replace question marks (`?`) with `cfqueryparam`-compatible structs for debugging.
* Preserve column case and order when converting a query to an array using the default `"array"` return format.
* Add a new [paginate](query-builder/executing-queries/retrieving-results.md#paginate) method to generate a pagination struct alongside the results.  This can be customized using a custom [PaginationCollector](query-builder/executing-queries/retrieving-results.md#custom-pagination-collectors).
* Allow raw values in [`insert`](query-builder/executing-queries/inserts-updates-deletes.md#insert) calls.
* Allow [default `queryExecute` `options`](query-builder/options-and-utilities/query-options.md#default-options) to be configure at a Query Builder level.  This also enables custom `QueryBuilders` a la [Hyper](https://www.forgebox.io/view/hyper).
* Add a [`whereLike`](query-builder/building-queries/wheres.md#wherelike) method.
* Allow closures to be used in left and right joins.
* Provide an [`addUpdate`](query-builder/executing-queries/inserts-updates-deletes.md#addupdate) method to programmatically build the `SET` clause of an update query.
* [Add a new `chunk` method](query-builder/executing-queries/retrieving-results.md#chunking-results) to grab records from the database in small sets.
* Add `raw` in `alterTable` segments.
* Add `dropAllObjects` support for `SqlServerGrammar` and `OracleGrammar` to support `migrate fresh` from cfmigrations.
* Add a `renameTable` alias for `rename`.
* Remove default constraints when dropping columns with a default on `SqlServerGrammar`.
* Add more column types and column helpers to `SchemaBuilder`, including:
  * `datetimeTz`
  * `lineString`
  * `nullableTimestamps`
  * `point`
  * `polygon`
  * `softDeletes`
  * `softDeletesTz`
  * `timeTz`
  * `timestamps`
  * `timestampTz`
  * `timestampsTz`
  * `withCurrent`

\*\*\*\*

## 6.4.0

* [Allow Expressions (`query.raw`) in update statements.](whats-new.md)
