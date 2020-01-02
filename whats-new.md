# What's New?

## 7.2.0

* Allow a [parent query](query-builder/options-and-utilities/parent-query.md) to be set.  A parent query will receive any method calls that are not found on the Query Builder instance.  This is especially useful for instances like [Quick](https://quick.ortusbooks.com/) to allow Quick features like scopes to be available inside any closures.

## 7.1.0

* Lambdas \(arrow functions\) are now allowed wherever closures are allowed.
* Add an [`orderByRaw`](query-builder/building-queries/ordering-grouping-and-limit.md#order-by-raw) method.
* Allow for fully-qualified column names \(`table_name.column.name`\) in the [`value`](query-builder/executing-queries/retrieving-results.md#value) and [`values`](query-builder/executing-queries/retrieving-results.md#values) methods.

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
* Add `defaultValue` and optional exception throwing to `value`. \(This changed the argument order.\)
* All methods that could conceivably take a subquery as well as a value now accept a closure or another builder instance to use as a subquery. \(This changed the argument names in some instances.\)

#### **Other Changes**

* Completely revamped documentation! \(You're looking at it right now.\)
* Add new flag to [`toSQL( showBindings = true )`](query-builder/debugging.md#tosql) to replace question marks \(`?`\) with `cfqueryparam`-compatible structs for debugging.
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

* [Allow Expressions \(`query.raw`\) in update statements.]()

