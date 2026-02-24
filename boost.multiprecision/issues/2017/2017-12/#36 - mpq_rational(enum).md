# #36 - mpq_rational(enum) [Open]

> Username: mglisse  
> Created at: 2017-12-29 10:33:24 UTC  
> Updated at: 2017-12-31 12:34:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/36  

```c++  
#include <boost/multiprecision/gmp.hpp>  
typedef boost::multiprecision::mpq_rational NT;  
enum E { neg = -1, zero, pos } e;  
int main(){  
  NT a = e;  
}  
```  
  
fails to compile, while it works for `cpp_rational`. (In my application, it is actually `q == e` that failed to compile, but I assume that is the same issue)

---

## Comment 1

> Username: mglisse  
> Created at: 2017-12-29 16:32:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/36#issuecomment-354468003  

Adding `|| boost::is_enum<V>::value` in the constructor of number let me move forward with the testing, but that might not be a good way to handle it.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2017-12-31 11:49:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/36#issuecomment-354599691  

It's as good a way as any, but I'm quite conflicted about this, consider:  
  
`enum  
{  
   big1 = -2,  
   big2 = ULLONG_MAX,  
};  
`  
  
What should happen when we assign from `big1` and `big2`?

---

## Comment 3

> Username: mglisse  
> Created at: 2017-12-31 12:34:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/36#issuecomment-354601440  

I would expect the same behavior when interoperating with an `enum E` or its `std::underlying_type_t<E>`. If that's `__int128`... well, I seem to get a compile error currently with `__int128`, so the same looks fine for the enum. But I haven't thought about it much, we only use very small enums in this project.
