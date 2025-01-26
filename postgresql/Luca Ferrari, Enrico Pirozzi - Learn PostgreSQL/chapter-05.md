## Learning about joins

### Using LATERAL JOIN

A lateral join is a type of join in SQL that allows you to join a table with a subquery, where the
subquery is run for each row of the main table. The subquery is executed before joining the rows
and the result is used to join the rows. With this join mode, you can use information from one
table to filter or process data from another table.

Suppose that we want to search for all users that have `posts` with `likes` greater than 2;
a query that solves this problem is:

```sql
select u.* from users u where exists (select 1 from posts p
where u.pk=p.author and likes > 2 ) ;
```

Let’s suppose now that we want the value of the `likes` field too. A simple way to solve this problem is 
using the `lateral` join:

```sql
select u.username,q.* from users u join lateral (select author,
title,likes from posts p where u.pk=p.author and likes > 2 ) as q on true;
```



