# #170 - Document of TIMESTAMP and "SET time_zone = 'UTC'" [Closed]

> Username: liubing  
> Created at: 2023-07-16 06:48:50 UTC  
> Updated at: 2023-07-17 12:35:25 UTC  
> Closed at: 2023-07-17 12:35:25 UTC  
> Url: https://github.com/boostorg/mysql/issues/170  

Quote  
```  
// This change has session scope. All operations after this query  
// will now use UTC for TIMESTAMPs. Other sessions will not see the change.  
// If you need to reconnect the connection, you need to run this again.  
conn.execute("SET @time_zone = 'UTC'", result);  
```  
  
I think the @ should be removed as it means user defined variable.

---

## Comment 1

> Username: anarthal  
> Created at: 2023-07-17 10:01:12 UTC  
> Url: https://github.com/boostorg/mysql/issues/170#issuecomment-1637757799  

Good catch. Thanks.
