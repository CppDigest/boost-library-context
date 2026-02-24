# #9 Qualify call to `mem_fn` [Merged]

> Username: ashtum  
> Created at: 2024-03-31 10:12:11 UTC  
> Updated at: 2024-03-31 14:23:43 UTC  
> Merged at: 2024-03-31 14:23:43 UTC  
> Closed at: 2024-03-31 14:23:43 UTC  
> Url: https://github.com/boostorg/compat/pull/9  

I don't know if this needs a test or not, but it is reproducible with the following:  
  
```C++  
#include <boost/compat/bind_front.hpp>  
#include <string>  
  
namespace compat = boost::compat;  
  
template<class T>  
struct S  
{  
  void f()  
  {  
  }  
};  
  
int main()  
{  
  S<std::string> s;  
  compat::bind_front(&S<std::string>::f, &s)();  
}  
```

---

## Comment 1

> Username: mclow  
> Created_at: 2024-03-31 14:16:06 UTC  
> Url: https://github.com/boostorg/compat/pull/9#issuecomment-2028763003  

That looks like a test to me. ;-)

---
