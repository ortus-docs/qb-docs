# README

![All Contributors](https://img.shields.io/badge/all_contributors-6-orange.svg?style=flat-square)

![Master Branch Build Status](https://img.shields.io/travis/coldbox-modules/qb/master.svg?style=flat-square&label=master)

![Development Branch Build Status](https://img.shields.io/travis/coldbox-modules/qb/development.svg?style=flat-square&label=development)

## Introduction

qb is a fluent query builder for CFML. It is **heavily** inspired by [Eloquent](https://laravel.com/docs/5.3/eloquent) from [Laravel](https://laravel.com/).

Using qb, you can:

* Quickly scaffold simple queries
* Make complex, out-of-order queries possible
* Abstract away differences between database engines

## Requirements

* Adobe ColdFusion 11+
* Lucee 4.5+

## Installation

Installation is easy through [CommandBox](https://www.ortussolutions.com/products/commandbox) and [ForgeBox](https://www.coldbox.org/forgebox). Simply type `box install qb` to get started.

## Code Samples

Compare these two examples:

```text
// Plain old CFML
q = queryExecute("SELECT * FROM users");

// qb
query = wirebox.getInstance('QueryBuilder@qb');
q = query.from('users').get();
```

The differences become even more stark when we introduce more complexity:

```text
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

```text
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

```text
moduleSettings = {
    qb = {
        defaultGrammar = "MySQLGrammar"
    }
};
```

If you are not using WireBox, just make sure to wire up the `Builder` object with the correct grammar:

```text
var grammar = new qb.models.Query.Grammars.MySQLGrammar();
var builder = new qb.models.Query.Builder( grammar );
```

### Return Format

You can influence the return format of the result in two ways.

By default, qb returns an array of structs as the result of your query. You can turn this behavior off by setting `builder.setReturningArrays( false )` for one-offs or setting `returningArrays = false` in your ColdBox config.

```text
moduleSettings = {
    qb = {
        returningArrays = false
    }
};
```

If you want complete control over your return result, you can provide a closure as a `returnFormat`. The results of the closure will be returned as the results of the builder.

```text
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

## Interception Points

Two interception points are available from QB: `preQBExecute` and `postQBExecute`. These fire before and after the `queryExecute` call, respectively. The following information is available in the `interceptData` struct:

| Name | Type | Description |
| :--- | :--- | :--- |
| sql | String | The sql string to execute |
| bindings | Struct | The struct of bindings \(keys and values\) for the query |
| options | Struct | Any options to pass along to `queryExecute` |

## Contributors

Thanks goes to these wonderful people \([emoji key](https://github.com/kentcdodds/all-contributors#emoji-key)\):

|  [📝](overview.md#blog-tonyjunkes) [📖](https://github.com/elpete/qb-docs/commits?author=tonyjunkes) |  [💬](overview.md#question-elpete) [📝](overview.md#blog-elpete) [🐛](https://github.com/elpete/qb-docs/issues?q=author%3Aelpete) [💻](https://github.com/elpete/qb-docs/commits?author=elpete) [🎨](overview.md#design-elpete) [📖](https://github.com/elpete/qb-docs/commits?author=elpete) [💡](overview.md#example-elpete) [👀](overview.md#review-elpete) [📢](overview.md#talk-elpete) [⚠️](https://github.com/elpete/qb-docs/commits?author=elpete) |  [🐛](https://github.com/elpete/qb-docs/issues?q=author%3ABluewaterSolutions) [💻](https://github.com/elpete/qb-docs/commits?author=BluewaterSolutions) [📖](https://github.com/elpete/qb-docs/commits?author=BluewaterSolutions) |  [🐛](https://github.com/elpete/qb-docs/issues?q=author%3Amurphydan) [💻](https://github.com/elpete/qb-docs/commits?author=murphydan) |  [💬](overview.md#question-aliaspooryorik) [💻](https://github.com/elpete/qb-docs/commits?author=aliaspooryorik) |  [💻](https://github.com/elpete/qb-docs/commits?author=timmaybrown) |
| :--- | :--- | :--- | :--- | :--- | :--- |


This project follows the [all-contributors](https://github.com/kentcdodds/all-contributors) specification. Contributions of any kind welcome!

