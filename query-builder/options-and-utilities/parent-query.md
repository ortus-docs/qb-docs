# Parent Query

If a parent query is set any methods not found on the Query Builder will be forwarded on to the `parentQuery`. This is especially useful for frameworks like [Quick](https://qb.ortusbooks.com) to allow Quick features like scopes inside any qb closure.

You can pass a parent query into the `init` method, set it using the `setParentQuery` method, and clear it using the `clearParentQuery` method.

