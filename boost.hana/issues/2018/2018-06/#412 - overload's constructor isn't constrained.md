# #412 - overload's constructor isn't constrained [Open]

> Username: brevzin  
> Created at: 2018-06-30 18:54:21 UTC  
> Updated at: 2018-06-30 21:12:12 UTC  
> Url: https://github.com/boostorg/hana/issues/412  

Short example:  
  
```  
#include "boost/hana.hpp"  
  
void f();  
void g(int);  
  
int main() {  
    auto a = boost::hana::overload(f,g);  
    auto b(a); // error  
}  
```  
  
This is because `overload_t`'s constructor doesn't have any constraints on its first parameter (need to do the `!is_same_v<uncref_t<F_>, overload_t>` dance).

---

## Comment 1

> Username: pfultz2  
> Created at: 2018-06-30 20:40:39 UTC  
> Url: https://github.com/boostorg/hana/issues/412#issuecomment-401565089  

There's always [`boost::hof::match`](http://boost-hof.readthedocs.io/en/latest/include/boost/hof/match.html), which doesn't have these issues.

---

## Comment 2

> Username: ricejasonf  
> Created at: 2018-06-30 21:12:12 UTC  
> Url: https://github.com/boostorg/hana/issues/412#issuecomment-401566550  

https://stackoverflow.com/questions/51117180/why-cant-boosthanaoverload-t-be-a-member-of-a-class
