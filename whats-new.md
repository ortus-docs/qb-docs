# What's New?

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

Enhance order by's with more direction options \([c767ac8](https://github.com/coldbox-modules/qb/commit/c767ac8764fab70d70dc77baa7bb9fb27c1d4eeb)\)

You can now use two shortcut methods: `orderByAsc` and `orderByDesc`. Additionally, `orderBySub` or using `orderBy` with a closure or builder instance will respect the direction argument.

## 7.3.15

* Fix using `whereBetween` with query param structs \([07c9b72](https://github.com/coldbox-modules/qb/commit/07c9b728bdbad6bf02ccd9d21dbdf6968062c02e)\)

## 7.3.14

* Ignore orders in aggregate queries \([39e1338](https://github.com/coldbox-modules/qb/commit/39e1338a147838165e05225bd91ef7e6cde2319a)\)

## 7.3.13

* Format with cfformat \([dc2a9b6](https://github.com/coldbox-modules/qb/commit/dc2a9b61503690d753a71c3b7bce002ebdf4ccda)\)

## 7.3.12

* Improve column wrapping with trimming \([d98a5cb](https://github.com/coldbox-modules/qb/commit/d98a5cb65851c154b6755e90254d1a2c1df82833)\)
* Prefer the parent query over magic methods when the parent query has the exact method. \([f9fd8d1](https://github.com/coldbox-modules/qb/commit/f9fd8d157cdc0d7480811c4659c130ee1d58888f)\)

## 7.3.9, 7.3.10, 7.3.11

* Switch to using [ForgeBox Storage](https://commandbox.ortusbooks.com/forgebox-enterprise/storage#storing-package-binaries-on-forgebox).

## 7.3.8

* Allow passing query options in to paginate \([cdecfb3](https://github.com/coldbox-modules/qb/commit/cdecfb36f5acab87edd3a478c570f77d285df554)\)

## 7.3.7

* Fix for inserting null values directly \([1de27a6](https://github.com/coldbox-modules/qb/commit/1de27a697f65bfdeed63442ad66be47cd0d30344)\)

## 7.3.5, 7.3.6

* Use cfformat for automatic formatting \([119e434](https://github.com/coldbox-modules/qb/commit/119e434b307a2cc2323b857a214c20842cafbbd4)\)
* Add a type to the onMissingMethod exception \([90d1093](https://github.com/coldbox-modules/qb/commit/90d109312b2ea86c00db34020b12b5ab22bb377b)\)

## 7.3.4

* Correctly wrap [comments](schema-builder/column-modifiers.md#comment) in `MySQLGrammar`.

## 7.3.2, 7.3.3

* Publish qb apidocs to [Ortus API Docs](https://apidocs.ortussolutions.com/#/coldbox-modules/qb/).

## 7.3.1

* Fix for null values breaking the new `checkIsActuallyNumeric` method in `QueryUtils`.

## 7.3.0

* Add a `parameterLimit` public property to `SqlServerGrammar`.  This property is used in Quick to split up eager loading to work around the 2100 param limit of SQL Server.

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

