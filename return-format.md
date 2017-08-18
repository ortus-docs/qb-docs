### Return Format

You can influence the return format of the result in two ways.

By default, qb returns an array of structs as the result of your query.  You can turn this behavior off by setting `builder.setReturningArrays( false )` for one-offs or setting `returningArrays = false` in your ColdBox config.

```
moduleSettings = {
    qb = {
        returningArrays = false
    }
};
```

If you want complete control over your return result, you can provide a closure as a `returnFormat`.  The results of the closure will be returned as the results of the builder.

```
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


