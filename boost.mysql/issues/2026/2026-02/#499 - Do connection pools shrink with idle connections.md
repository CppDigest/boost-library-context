# #499 - Do connection pools shrink with idle connections? [Open]

> Username: bruno-viva  
> Created at: 2026-02-05 00:46:08 UTC  
> Updated at: 2026-02-05 17:02:00 UTC  
> Url: https://github.com/boostorg/mysql/issues/499  

Hi,  
  
I launched my migration to boost mysql recently and I noticed that once the connection count gets to the maximum, the "threads connected" metric in the MySql server doesn't go down. Does that mean that the connection pool never shrinks? Or did I miss something?  
  
Thanks in advance!

---

## Comment 1

> Username: anarthal  
> Created at: 2026-02-05 13:38:54 UTC  
> Url: https://github.com/boostorg/mysql/issues/499#issuecomment-3853734312  

No, they never shrink, at least at the moment. But I'm open to implementing a shrinking algorithm. What kind of algorithm would you expect? Looking at [similar algorithms](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html), maybe a `idle_period` after which connections above `initial_size` that haven't been used are closed and destroyed?

---

## Comment 2

> Username: bruno-viva  
> Created at: 2026-02-05 17:02:00 UTC  
> Url: https://github.com/boostorg/mysql/issues/499#issuecomment-3854951211  

Thanks @anarthal . Sounds good to me!
