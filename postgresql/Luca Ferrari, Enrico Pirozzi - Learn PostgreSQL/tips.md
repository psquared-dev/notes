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


