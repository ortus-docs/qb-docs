# Introduction

![Master Branch Build Status](https://img.shields.io/travis/coldbox-modules/qb/master.svg?style=flat-square\&label=master)

![Development Branch Build Status](https://img.shields.io/travis/coldbox-modules/qb/development.svg?style=flat-square\&label=development)

## Introduction

qb is a fluent query builder for CFML. It is **heavily** inspired by [Eloquent](https://laravel.com/docs/5.3/eloquent) from [Laravel](https://laravel.com/).

Using qb, you can:

* Quickly scaffold simple queries
* Make complex, out-of-order queries possible
* Abstract away differences between database engines

## Requirements

* Adobe ColdFusion 2016+
* Lucee 5+

qb supports four major database grammars:

* MySQL (`MySQLGrammar`)
* Oracle (`OracleGrammar`)
* Postgres (`PostgresGrammar`)
* Microsoft SQL Server (`SqlServerGrammar`)

### Discussion & Help

The Box modules discussion group and community can be found here:

[https://community.ortussolutions.com/c/box-modules/qb/27](https://community.ortussolutions.com/c/box-modules/qb/27)

## Installation

Installation is easy through [CommandBox](https://www.ortussolutions.com/products/commandbox) and [ForgeBox](https://www.coldbox.org/forgebox). Simply type `box install qb` to get started.

## Code Samples

Compare these two examples:

```
// Plain old CFML
q = queryExecute("SELECT * FROM users");

// qb
query = wirebox.getInstance('QueryBuilder@qb');
q = query.from('users').get();
```

The differences become even more stark when we introduce more complexity:

```
// Plain old CFML
q = queryExecute(
    "SELECT * FROM posts WHERE published_at IS NOT NULL AND author_id IN ?",
    [ { value = '5,10,27', cfsqltype = 'CF_SQL_NUMERIC', list = true } ]
);

// qb
query = wirebox.getInstance('QueryBuilder@qb');
q = query.from('posts')
         .whereNotNull('published_at')
         .whereIn('author_id', [5, 10, 27])
         .get();
```

With qb you can easily handle setting order by statements before the columns you want or join statements after a where clause:

```
query = wirebox.getInstance('QueryBuilder@qb');
q = query.from('posts')
         .orderBy('published_at')
         .select('post_id', 'author_id', 'title', 'body')
         .whereLike('author', 'Ja%')
         .join('authors', 'authors.id', '=', 'posts.author_id')
         .get();

// Becomes

q = queryExecute(
    "SELECT post_id, author_id, title, body FROM posts INNER JOIN authors ON authors.id = posts.author_id WHERE author LIKE ? ORDER BY published_at",
    [ { value = 'Ja%', cfsqltype = 'CF_SQL_VARCHAR', list = false, null = false } ]
);
```

qb enables you to explore new ways of organizing your code by letting you pass around a query builder object that will compile down to the right SQL without you having to keep track of the order, whitespace, or other SQL gotchas!

Here's a gist with an example of the powerful models you can create with this! [https://gist.github.com/elpete/80d641b98025f16059f6476561d88202](https://gist.github.com/elpete/80d641b98025f16059f6476561d88202)

## Usage

To start a new query, instantiate a new Builder: `wirebox.getInstance('QueryBuilder@qb')`.

By default, qb uses a generic Grammar. You can specify your specific grammar in ColdBox by setting the `defaultGrammar` in your `moduleSettings`.

```
moduleSettings = {
    qb = {
        defaultGrammar = "MySQLGrammar@qb"
    }
};
```

If you are not using WireBox, just make sure to wire up the `Builder` object with the correct grammar:

```
var grammar = new qb.models.Query.Grammars.MySQLGrammar();
var builder = new qb.models.Query.Builder( grammar );
```
