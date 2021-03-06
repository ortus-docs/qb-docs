# Installation & Usage

## Installation

Installation is easy through [CommandBox](https://www.ortussolutions.com/products/commandbox) and [ForgeBox](https://www.forgebox.io/). Simply type `box install qb` to get started.

## Usage

To start a new query, instantiate a new Builder: `wirebox.getInstance('QueryBuilder@qb')`.

By default, qb uses a generic Grammar. You can specify your specific grammar in ColdBox by setting the `defaultGrammar` in your `moduleSettings`.

```text
moduleSettings = {
    qb = {
        defaultGrammar = "MySQLGrammar@qb"
    }
};
```

The grammars provided by qb are:

* MySQLGrammar
* OracleGrammar
* PostgresGrammar
* SqlServerGrammar

If you are not using WireBox, just make sure to wire up the `Builder` object with the correct grammar:

```text
var grammar = new qb.models.Grammars.MySQLGrammar();
var builder = new qb.models.Query.QueryBuilder( grammar );
```

## SQL Type Inferral

QB binds all parameters by default and guesses the SQL type based on passed values. The default SQL type for numeric values is `CF_SQL_NUMERIC`, which is a floating point number, for the widest compatibility. This can cause performance problems with large recordsets in some database engines. You can provide a different default in coldbox.cfc if you wish to override this setting:

```text
moduleSettings = {
    qb = {
        defaultGrammar = "MySQLGrammar@qb",
        numericSQLType = "CF_SQL_BIGINT"
    }
};
```

## Integrating With FW/1

> Note: These instructions assume a basic knowledge of FW/1, a working FW/1 application structure with qb installed in the `/subsystems` directory \(manually or via CommandBox\), and a database configured to run with your application.

### Wiring Up With DI/1

Once the application structure is setup, now we need to wire up qb to a bean factory using DI/1.

First we will add a mapping in `Application.cfc`.

```text
this.mappings = {
    "/qb" = expandPath("./subsystems/qb")
};
```

Next we need to tell DI/1 where qb's components are and how to reference them for later use in the application. We can do so by defining the configuration settings in the `variables.framework.subsystems` struct in `Application.cfc`. The example below makes use of a load listener to declare each component instance and pass in any constructor arguments.

```text
qb = {
  diLocations = "/qb/models",
  diConfig = {
    loadListener = function( di1 ) {
      di1.declare( "BaseGrammar" ).instanceOf( "qb.models.Query.Grammars.Grammar" ).done()
         .declare( "MySQLGrammar" ).instanceOf( "qb.models.Query.Grammars.MySQLGrammar" ).done()
         .declare( "QueryUtils" ).instanceOf( "qb.models.Query.QueryUtils" ).done()
         .declare( "QueryBuilder" ).instanceOf( "qb.models.Query.QueryBuilder" )
         .withOverrides({
            grammar = di1.getBean( "MySQLGrammar" ),
            utils = di1.getBean( "QueryUtils" ),
            returnFormat = "array"
         })
         .asTransient();
    }
  }
}
```

### Usage In Your FW/1 Application

Now that everything is configured, you can launch your application with CommandBox by entering `start` in the terminal or use whatever method you're accustomed to.

To access qb from your application's code, you can call on it by using `getBeanFactory()`.

```text
// Create an instance of qb
builder = getBeanFactory( "qb" ).getBean( "QueryBuilder" );
// Query the database
posts = builder.from( "Posts" ).get();
posts = builder.from( "Posts" ).where( "IsDraft", "=", 0 ).get();
```

#### For further instructions on getting started with qb & FW/1, refer to [this blog post](http://tonyjunkes.com/blog/working-with-fw1-and-qb/).

