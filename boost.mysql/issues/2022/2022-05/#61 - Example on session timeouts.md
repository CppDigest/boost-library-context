# #61 - Example on session timeouts [Open]

> Username: anarthal  
> Created at: 2022-05-11 11:15:33 UTC  
> Updated at: 2022-05-11 11:15:33 UTC  
> Url: https://github.com/boostorg/mysql/issues/61  

Write an example demonstrating how to use the `wait_timeout` session variable to modify session timeout:  
  
```  
conn.query("SET SESSION wait_timeout = 3600");  
```
