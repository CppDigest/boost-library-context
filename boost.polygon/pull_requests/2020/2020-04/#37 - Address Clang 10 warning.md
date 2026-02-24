# #37 Address Clang 10 [-Wdeprecated-copy] warning. [Merged]

> Username: helixhorned  
> Created at: 2020-04-15 11:36:10 UTC  
> Updated at: 2020-04-15 20:44:40 UTC  
> Merged at: 2020-04-15 19:59:22 UTC  
> Closed at: 2020-04-15 19:59:22 UTC  
> Url: https://github.com/boostorg/polygon/pull/37  

("definition of implicit copy constructor for 'robust_fpt<double>' is deprecated  
because it has a user-declared copy assignment operator")  
  
----  
  
One of two warnings that I encountered when building with LLVM 10.  
  
The other one is:  
  
```  
boost-polygon/polygon_45_set_data.hpp:1662:24: warning: local variable 'str' will be copied despite being thrown by name [-Wreturn-std-move]  
        } else { throw str; }  
                       ^~~  
boost/polygon/polygon_45_set_data.hpp:1662:24: note: call 'std::move' explicitly to avoid copying  
        } else { throw str; }  
                       ^~~  
                       std::move(str)  
```  
(And again on line 1773.)  
  
However, I omitted that one because I don't know how far back in terms of C++ Standard Boost aims to be compatible with. Probably there is a macro wrapping `std::move`?

---

## Comment 1

> Username: asydorchuk  
> Created_at: 2020-04-15 19:59:19 UTC  
> Url: https://github.com/boostorg/polygon/pull/37#issuecomment-614250520  

Thanks! Merged.

---

## Comment 2

> Username: glenfe  
> Created_at: 2020-04-15 20:44:40 UTC  
> Url: https://github.com/boostorg/polygon/pull/37#issuecomment-614270518  

This isn't the right change. Instead of explicitly adding a copy constructor here we should be removing the explicit `operator=`.  When both are implicitly generated you shouldn't get this warning.

---
