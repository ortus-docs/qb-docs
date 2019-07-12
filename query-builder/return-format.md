# Return Format

You can influence the return format of the result in two ways.

By default, qb returns an array of structs as the result of your query. This is the same as specifiying `array` as your `returnFormat`:

```javascript
moduleSettings = {
    qb = {
        returnFormat = "array"
    }
};
```

You can get the original query object that CFML generates by setting the `returnFormat` to `query`:

```javascript
moduleSettings = {
    qb = {
        returnFormat = "query"
    }
};
```

If you want complete control over your return result, you can provide a closure as a `returnFormat`. The results of the closure will be returned as the results of the builder.

```javascript
moduleSettings = {
    qb = {
        returnFormat = function( q ) {
            return application.wirebox.getInstance(
                name = "Collection",
                initArguments = { collection = q }
            );
        }
    }
};
```

