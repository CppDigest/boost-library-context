# #1087 - Intrusive list iterator declaration for incomplete type does not compile (std::list and boost::container::list do)... [Open]

> Username: nbooster  
> Created at: 2025-09-30 13:54:15 UTC  
> Updated at: 2025-10-31 10:49:01 UTC  
> Url: https://github.com/boostorg/boost/issues/1087  

Hello,  
  
I just want to ask if there is a way to make this code compile:  
  
```cpp  
// example.hpp  
#include <list>  
#include <boost/container/list.hpp>  
#include <boost/intrusive/list.hpp>  
  
class LL;  
  
class O  
{  
    std::list<LL>::iterator stlIt;  
    boost::container::list<LL>::iterator boostIt;  
    // uncommenting this line the code does not compile...  
    // boost::intrusive::list<LL>::iterator boostIntrusiveIt;  
};  
  
class OL  
{  
    O member;  
};  
  
class L  
{  
    OL member;  
};  
  
class LL  
{  
    L member;  
    boost::intrusive::list_member_hook<boost::intrusive::link_mode<boost::intrusive::normal_link>> listHook_;  
};  
```  
  
g++ 15.2.0 (Boost 1.88)...  
  
The normal list iterators seem to be OK,  
but the intrusive one is not...  
  
*What about SCARY iterators ( https://www.boost.org/doc/libs/1_70_0/doc/html/intrusive/boost_intrusive_iterators.html ) ?  
**How fast is this: s_iterator_to ??

---

## Comment 1

> Username: nigels-com  
> Created at: 2025-10-31 10:44:37 UTC  
> Url: https://github.com/boostorg/boost/issues/1087#issuecomment-3472459613  

I suggest taking this question over to [boostorg/intrusive](https://github.com/boostorg/intrusive/issues).  
This doesn't seem like the right place for this issue.

---

## Comment 2

> Username: nbooster  
> Created at: 2025-10-31 10:49:01 UTC  
> Url: https://github.com/boostorg/boost/issues/1087#issuecomment-3472478836  

I just did...  
Although here is going to be seen by more people...  
Anyway, you can close the issue if you like...
