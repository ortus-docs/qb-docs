## Usage

To start a new query, instantiate a new Builder: `wirebox.getInstance('Builder@qb')`.

By default, qb uses a generic Grammar.  You can specify your specific grammar in ColdBox by setting the `defaultGrammar` in your `moduleSettings`.

```
moduleSettings = {
    qb = {
        defaultGrammar = "MySQLGrammar"
    }
};
```

If you are not using WireBox, just make sure to wire up the `Builder` object with the correct grammar:

```
var grammar = new qb.models.Query.Grammars.MySQLGrammar();
var builder = new qb.models.Query.Builder( grammar );
```

