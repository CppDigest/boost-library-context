# #24 Fix -Wdeprecated-copy (Clang, but partly GCC) [Merged]

> Username: Kojoley  
> Created at: 2020-04-30 00:58:54 UTC  
> Updated at: 2021-01-28 11:15:38 UTC  
> Merged at: 2020-04-30 22:59:32 UTC  
> Closed at: 2020-04-30 22:59:32 UTC  
> Url: https://github.com/boostorg/proto/pull/24  

Initially I was planning to suppress the warning inside `BOOST_PROTO_EXTENDS_COPY_ASSIGN_` macro too since there is no way to fix it for aggregate types, but it is probably an overkill, hopefully in Phoenix there is only one place where it is needed and in Spirit copy constructors are explicitly defined.  
  
Fixes #22

---

## Comment 1

> Username: k15tfu  
> Created_at: 2021-01-28 11:11:50 UTC  
> Updated_at: 2021-01-28 11:15:38 UTC  
> Url: https://github.com/boostorg/proto/pull/24#issuecomment-768980603  

@Kojoley @ericniebler Hi! This causes another warning on AppleClang 12:  
```  
boost/proto/expr.hpp:140:44: error: unknown warning group '-Wdeprecated-copy'  
```  
  
We can check if this warning exists first: `__has_warning("-Wdeprecated-copy")`

---
