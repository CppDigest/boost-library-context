# #69 - Query formatting [Closed]

> Username: anarthal  
> Created at: 2022-05-12 10:57:40 UTC  
> Updated at: 2024-02-18 11:34:02 UTC  
> Closed at: 2024-02-18 11:34:02 UTC  
> Url: https://github.com/boostorg/mysql/issues/69  

Allow executing a one-off query with parameters without requiring the user to compose the query by string concatenation.  
  
Ideas:  
* Provide something similar to [mysql_real_escape_string](https://dev.mysql.com/doc/c-api/8.0/en/mysql-real-escape-string.html).  
* Create, execute and close a prepared statement.  
  
The ideal syntax would be something like:  
  
```  
conn.query( "SELECT * FROM tb_something WHERE id = ?", id );  
```
