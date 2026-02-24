# #175 - Investigate basic_streambuf::consume(0) [Closed]

> Username: vinniefalco  
> Created at: 2016-11-08 12:04:47 UTC  
> Updated at: 2017-05-07 23:14:15 UTC  
> Closed at: 2017-05-07 23:14:15 UTC  
> Url: https://github.com/boostorg/beast/issues/175  

#159 reports that this change fixed a 100% CPU utilization problem in `parse_op`  
```  
                 return;  
              }  
              if(used > 0)  
 -                d.started = true;  
 -            d.db.consume(used);  
 +            {  
 +                d.got_some = true;  
 +                d.db.consume(used);  
 +            }  
              if(d.p.complete())  
              {  
                  // call handler  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-05-07 23:14:15 UTC  
> Url: https://github.com/boostorg/beast/issues/175#issuecomment-299742586  

Seems its no longer a problem
