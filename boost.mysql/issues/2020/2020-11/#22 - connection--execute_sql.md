# #22 - connection::execute_sql [Closed]

> Username: anarthal  
> Created at: 2020-11-29 17:42:31 UTC  
> Updated at: 2023-01-14 23:06:44 UTC  
> Closed at: 2023-01-14 23:06:44 UTC  
> Url: https://github.com/boostorg/mysql/issues/22  

Provide a helper functions to make these cases easier:  
  
* A query returning a single value, e.g. a `SELECT COUNT(*)` statement.  
* A query returning an empty resultset, e.g. `INSERT` or `DELETE`.  
* A single row.  
* Zero or one rows.  
* A single column.
