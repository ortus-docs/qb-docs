# Migration Guide

## v7.0.0

Support for Lucee 4.5 and Adobe ColdFusion 11 has been dropped.  If you need support for these engines, please remain on an earlier version of qb.

### Variadic Parameters Support Removed

Variadic parameter support was the ability to pass any number of arguments to certain methods like `select`.

```javascript
qb.select( "name", "email", "createdDate" );
```

This code came with a slight performance cost and readability cost. That, combined with the fact that the above syntax is very close to an array, we are dropping support for variadic parameters.  To migrate, wrap instances of variadic parameters in an array:

```javascript
qb.select( [ "name", "email", "createdDate" ] );
```

### defaultGrammar updated to be the full WireBox mapping

In previous versions, the value passed to `defaultGrammar` was used to look up a mapping in the `@qb` namespace.  This made it difficult to add or use grammars that weren't part of qb. \(You could get around this be registering your custom grammar in the `@qb` namespace, but doing so seemed strange.\)

To migrate this code, change your `defaultGrammar` to be the full WireBox mapping in your `moduleSettings`:

```javascript
moduleSettings = {
    "qb": {
        "defaultGrammar": "MSSQLGrammar@qb"
    }
};
```

### value method argument order changed

A `defaultValue` parameter and optional exception throwing was added to `value`.  This pushed the `options` struct to the end of the method.  If you are using positional parameters with `value`, you will need to update your method calls to either use named parameters or the new positions.

```javascript
public any function value(
    required string column,
    string defaultValue = "",
    boolean throwWhenNotFound = false,
    struct options = {}
);
```

### Some methods renamed `callback` to `query`

All methods that could conceivably take a subquery as well as a value now accept a closure or another builder instance to use as a subquery. This led to changing the `callback` argument to `query` in the following cases:

* `whereSub`
* `whereInSub`
* `whereExists`
* `orWhereExists`
* `whereNotExists`
* `andWhereNotExists`
* `orWhereNotExists`
* `whereNullSub`
* `orderBySub`
* `subSelect`

If you are using named parameters with any of the above methods you will need to migrate your method calls.

## v5.0.0

Version `v5.0.0` brings support for `SchemaBuilder` inside `qb`. To avoid naming confusion, `Builder` was renamed to `QueryBuilder`. Any references in your code to `Builder@qb` need to be updated to `QueryBuilder@qb`.

