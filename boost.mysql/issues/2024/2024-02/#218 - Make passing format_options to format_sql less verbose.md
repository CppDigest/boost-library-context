# #218 - Make passing format_options to format_sql less verbose [Closed]

> Username: anarthal  
> Created at: 2024-02-15 15:52:20 UTC  
> Updated at: 2024-08-01 10:59:57 UTC  
> Closed at: 2024-08-01 10:59:57 UTC  
> Url: https://github.com/boostorg/mysql/issues/218  

Currently it's  
```  
format_sql(conn.format_opts().value(), "SELECT {}", 42);  
```  
  
We can do better and provide some wrapper like:  
  
```  
format_sql(conn, "SELECT {}", 42);  
conn.format_sql("SELECT {}", 42);  
conn.execute(query("SELECT {}", 42));  
```
