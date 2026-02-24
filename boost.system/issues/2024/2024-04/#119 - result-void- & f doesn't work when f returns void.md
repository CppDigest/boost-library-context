# #119 - result<void> & f doesn't work when f returns void [Closed]

> Username: grisumbras  
> Created at: 2024-04-05 19:06:52 UTC  
> Updated at: 2024-04-09 02:32:11 UTC  
> Closed at: 2024-04-09 02:32:11 UTC  
> Url: https://github.com/boostorg/system/issues/119  

E.g.  
```c++  
boost::system::result<void> rv;  
rv & []{ /* do nothing */ };  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2024-04-06 18:47:58 UTC  
> Url: https://github.com/boostorg/system/issues/119#issuecomment-2041165424  

Looks like `result<nonvoid> & f` doesn't work either in that case.

---

## Comment 2

> Username: pdimov  
> Created at: 2024-04-09 02:32:11 UTC  
> Url: https://github.com/boostorg/system/issues/119#issuecomment-2044045719  

Should be fixed on develop.
