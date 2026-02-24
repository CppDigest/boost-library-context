# #191 Ignore unused argument warning with clang. [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2019-01-03 09:49:04 UTC  
> Updated at: 2019-01-03 10:13:32 UTC  
> Merged at: 2019-01-03 10:13:32 UTC  
> Closed at: 2019-01-03 10:13:32 UTC  
> Url: https://github.com/boostorg/fiber/pull/191  

This fixes the following warning:  
  
```  
/remote/intdeliv/components/osp/Boost/18-0-0-6/include/boost/fiber/context.hpp:422:36: error: unused parameter 'c' [-Werror,-Wunused-parameter]  
    run_( boost::context::fiber && c) {  
                                   ^  
1 error generated.  
```

---

## Comment 1

> Username: olk  
> Created_at: 2019-01-03 10:12:58 UTC  
> Url: https://github.com/boostorg/fiber/pull/191#issuecomment-451102069  

ty

---
