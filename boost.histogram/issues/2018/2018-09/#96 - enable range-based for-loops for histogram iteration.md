# #96 - enable range-based for-loops for histogram iteration [Closed]

> Username: HDembinski  
> Created at: 2018-09-21 09:01:16 UTC  
> Updated at: 2018-11-26 17:01:13 UTC  
> Closed at: 2018-11-26 17:01:13 UTC  
> Url: https://github.com/boostorg/histogram/issues/96  

```  
You could have something like this:  
  
for(auto&& v : h)     // alternatively: for(auto&& v :  indices_and_values_view(h))   
{  
 std::cout << v.idx(0) << ' ' << v.idx(1) << ' ' << v.value() << std::endl;  
}  
  
Instead of:  
  
for (auto it = h.begin(); it != h.end(); ++it)   
{  
 std::cout << it.idx(0) << ' ' << it.idx(1) << ' ' << it->value() << std::endl;  
}  
```

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-11-26 17:01:13 UTC  
> Url: https://github.com/boostorg/histogram/issues/96#issuecomment-441715882  

This is implemented in the develop branch now, called `indexed`
