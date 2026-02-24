# #14 - is_aggregate issue [Closed]

> Username: Eddie-cz  
> Created at: 2022-07-25 10:22:23 UTC  
> Updated at: 2022-07-26 07:38:18 UTC  
> Closed at: 2022-07-26 07:38:18 UTC  
> Url: https://github.com/boostorg/redis/issues/14  

Hello, it's problematic to use custom functions like is_aggregate(type t), which have same name as std counterparts. Simple "using namespace std" in project will make aedis uncompilable. Please rename affected fuction.  
  
Error: aedis/adapter/detail/adapters.hpp(148,11): error : no viable constructor or deduction guide for deduction of template arguments of 'is_aggregate'

---

## Comment 1

> Username: mzimbres  
> Created at: 2022-07-25 20:28:34 UTC  
> Url: https://github.com/boostorg/redis/issues/14#issuecomment-1194588663  

That is no Aedis fault. Fixing the code that pulls `std::is_aggregate` into global namespace is the recommended way of solving this.

---

## Comment 2

> Username: Eddie-cz  
> Created at: 2022-07-26 07:38:18 UTC  
> Url: https://github.com/boostorg/redis/issues/14#issuecomment-1195119919  

While yey, it's taken as bad habit, I still think aedis woulnd't be worse with explixitly namespacing this private library function.
