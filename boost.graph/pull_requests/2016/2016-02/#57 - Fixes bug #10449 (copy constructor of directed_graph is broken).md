# #57 Fixes bug #10449 (copy constructor of directed_graph is broken) [Merged]

> Username: arneboe  
> Created at: 2016-02-24 10:14:19 UTC  
> Updated at: 2016-04-23 22:15:42 UTC  
> Merged at: 2016-04-23 22:15:42 UTC  
> Closed at: 2016-04-23 22:15:42 UTC  
> Url: https://github.com/boostorg/graph/pull/57  

Without this patch it is not possible to call the copy constructor of directed_graph.  
I.e. the following code will not compile without the fix:  
  
```  
boost::directed_graph<> d;  
boost::directed_graph<> d2(d);  
```

---
