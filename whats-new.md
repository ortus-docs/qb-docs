# What's New?

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
* Add new flag to `toSQL( showBindings = true )` to replace question marks \(`?`\) with `cfqueryparam`-compatible structs for debugging.
* Add a new paginate method to generate a pagination struct alongside the results.  This can be customized using a custom PaginationCollector.
* Allow raw values in `insert` calls.
* Allow default `queryExecute` `options` to be configure at a Query Builder level.  This also enables custom `QueryBuilders` a la [Hyper](https://www.forgebox.io/view/hyper).
* Add a `whereLike` method.
* Allow closures to be used in left and right joins.
* Provide an `addUpdate` method to programmatically build the `SET` clause of an update query.
* [Add a new `chunk` method](query-builder/executing-queries/retrieving-results.md#chunking-results) to grab records from the database in small sets.
* Add `raw` in `alterTable` segments.
* Add `dropAllObjects` support for `MSSQLGrammar` and `OracleGrammar` to support `migrate fresh`.
* Add a `renameTable` alias for `rename`.
* Rename default constraints when dropping columns with a default on `MSSQLGrammar`.
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
  * ```withCurrent```

\*\*\*\*

## 6.4.0

* [Allow Expressions \(`query.raw`\) in update statements.]()

