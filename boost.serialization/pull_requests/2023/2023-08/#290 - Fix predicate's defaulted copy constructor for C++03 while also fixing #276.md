# #290 Fix predicate's defaulted copy constructor for C++03 while also fixing #276. [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2023-08-25 13:02:21 UTC  
> Updated at: 2023-08-29 22:51:02 UTC  
> Closed at: 2023-08-29 22:50:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/290  

Hi,  
  
This fixes the issue I introduced in #276 (sorry for the breakage).  
  
This time I tested not directly with boost, but using compiler explorer. This is the code I tested:  
  
```  
#include "boost/config.hpp"      
  
struct predicate {  
    BOOST_DEFAULTED_FUNCTION(predicate(const predicate& rhs), : m_ti(rhs.m_ti) {})  
    BOOST_DELETED_FUNCTION(predicate & operator=(const predicate & rhs))  
public:  
    const void * const m_ti;  
    /*  
    bool operator()(helper_value_type const &rhs) const {  
        return m_ti == rhs.first;  
    }  
    */  
    predicate(const void * ti) :  
        m_ti(ti)  
    {}    
};  
  
void f(const predicate& a)  
{  
    predicate b(a);  
    (void)b;  
}  
```  
  
I tried with flags `-Wall -Wextra -Werror -std=gnu++03` and `-Wall -Wextra -Werror -std=gnu++23`, and with both gcc and clang x86_64 (trunk version in both cases). There is no compilation error in all these 4 different variants.  
  
Compiler explorer link for clang/C++03: https://godbolt.org/z/PhPGsMdoM  
  
Cheers,  
Romain

---

## Comment 1

> Username: Romain-Geissler-1A  
> Created_at: 2023-08-25 13:21:03 UTC  
> Updated_at: 2023-08-25 13:23:21 UTC  
> Url: https://github.com/boostorg/serialization/pull/290#issuecomment-1693353738  

Note: given that you use Boost filesystem in your tests (but not in the core library), and given that Boost filesystem 1.84 will drop C++03 support, you may hit some issues testing C++03 in the future.  
  
Same for Boost variant (which is used only in `variant.hpp`), and potentially other libraries not fully necessary for the core of boost serialization.

---

## Comment 2

> Username: robertramey  
> Created_at: 2023-08-29 22:51:02 UTC  
> Url: https://github.com/boostorg/serialization/pull/290#issuecomment-1698245182  

merged and tested

---
