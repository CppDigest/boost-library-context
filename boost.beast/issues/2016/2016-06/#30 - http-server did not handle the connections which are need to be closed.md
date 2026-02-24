# #30 - http-server did not handle  the connections which are need to be closed. [Closed]

> Username: junjiexing  
> Created at: 2016-06-20 09:14:59 UTC  
> Updated at: 2016-07-08 00:43:49 UTC  
> Closed at: 2016-07-08 00:43:49 UTC  
> Url: https://github.com/boostorg/beast/issues/30  

When I use Apache Bench to test the performance of beast, however i found that   
the connection did not closed, this leading the test failed.  
how to repair the problem?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-06-20 13:17:33 UTC  
> Url: https://github.com/boostorg/beast/issues/30#issuecomment-227139072  

I'm looking into it

---

## Comment 2

> Username: vinniefalco  
> Created at: 2016-07-08 00:43:03 UTC  
> Updated at: 2016-07-08 00:43:18 UTC  
> Url: https://github.com/boostorg/beast/issues/30#issuecomment-231247602  

I believe the problem has been resolved in **1.0.0-b7** can you please try it?
