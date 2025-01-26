## Printing Null Values

By default, `NULL` values are displayed as an empty space. This behavior can be changed using the 
following command:

```sql
\pset null NULL
```

How It Works:
* `\pset`: Stands for "print settings." It is used to control how query results are formatted in psql.
* `null`: This specifies that you're setting the display format for `NULL` values.
* `NULL`: The string you want to use to represent `NULL` values in the output. It could be any text, not just `NULL`.


## Deleting with TRUNCATE

Deleting all records with `TRUNCATE` is must faster than the `DELETE`. In the `TRUNCATE` command, it is not possible to use `WHERE` conditions.


## USE JOINS

Using the `INNER JOIN` condition, we can rewrite all queries that can be written using
the `IN` or `EXISTS` condition.

It is preferable to use `JOIN` conditions whenever possible instead of `IN` or `EXISTS` conditions, be-
cause they perform better in terms of execution speed.

Example:

```sql
select * from categories c where c.`pk` not in (select category
from posts);
```

This query, written using the `NOT IN` condition, looks for all records in the `categories` table for
which the `pk` value does not match in the category field of the posts table. As we have already
seen, another way to write the same query would be to use the `NOT EXISTS` condition:

```sql
select * from categories c where not exists (select 1 from posts
where category=c.pk);
```

If we now wanted to use a left join in order to achieve the same purpose, we would start by writing
the following left join query:

```sql
select c.*,p.category from categories c left join posts p on
p.category=c.pk where p.category is null
```

As shown here, we get the same result we had using the `NOT EXISTS` or `NOT IN` condition.




