# Column Formatter

Available as an advanced option for framework authors, qb will call out to a column formatter prior to processing a column as part of the SQL query.  This allows frameworks like Quick to define queries using aliases and transform them to columns during execution.

You can provide your own column formatter function to qb through the `init` method or by calling `setColumnFormatter`.  It is a function that takes a column string and returns a string

```javascript
query.setColumnFormatter( function( column ) {
    return lcase( arguments.column );
} );
```

