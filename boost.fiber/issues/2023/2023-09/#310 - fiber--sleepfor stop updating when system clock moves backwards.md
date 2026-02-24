# #310 - fiber::sleepfor stop updating when system clock moves backwards [Open]

> Username: arc130  
> Created at: 2023-09-26 15:37:23 UTC  
> Updated at: 2023-09-26 15:37:23 UTC  
> Url: https://github.com/boostorg/fiber/issues/310  

I'm using fiber with shared_work algo on boost 1.78 aarch64.  
a simple code is  
```  
while (true)｛  
  this_fiber::sleep_for(100ms);  
  
｝  
```  
  
when system clock moves backwards, this fiber would never resumed.
