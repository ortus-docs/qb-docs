# Limit, Offset, and Pagination

## limit

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| value | numeric | `true` |  | The limit value for the query. |

Sets the limit value for the query.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .limit( 5 );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
LIMIT 5
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## take

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| value | numeric | `true` |  | The limit value for the query. |

Sets the limit value for the query.  Alias for [`limit`](limit-offset-and-pagination.md#limit).

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .take( 5 );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
LIMIT 5
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## offset

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| value | numeric | `true` |  | The offset value for the query. |

Sets the offset value for the query.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .offset( 25 );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
OFFSET 25
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## forPage

| Name | Type | Required | Default | Description |
| :--- | :--- | :--- | :--- | :--- |
| page | numeric | `true` |  | The page number to retrieve. |
| maxRows | numeric | `true` |  | The number of records per page.  If a number less than 0 is passed, 0 is used instead. |

Helper method to calculate the limit and offset given a page number and count per page.

{% code-tabs %}
{% code-tabs-item title="QueryBuilder" %}
```javascript
query.from( "users" )
    .forPage( 3, 15 );
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="MySQL" %}
```sql
SELECT *
FROM `users`
LIMIT 15
OFFSET 30
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## paginate

This method combines `forPage`, `count`, and `get` to create a pagination struct alongside the results. Information on the `paginate` method, including custom pagination collectors, can be found in the [Retreiving Results](../executing-queries/retrieving-results.md#paginate) section of the documentation.



