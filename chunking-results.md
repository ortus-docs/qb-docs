### Chunking Results

If you need to work with thousands of database records, consider using the `chunk` method. This method retrieves a small chunk of the results at a time and feeds each chunk into a `Closure` for processing. This method is very useful for writing background tasks that process thousands of records. For example, let's work with the entire `users` table in chunks of 100 records at a time:
```
**need sample here**
```






