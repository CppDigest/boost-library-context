# #90 - unqualified call to `get<T>` with a non-`boost::optional` type should not match [Open]

> Username: Kojoley  
> Created at: 2021-02-27 00:30:07 UTC  
> Updated at: 2021-03-04 20:46:51 UTC  
> Url: https://github.com/boostorg/optional/issues/90  

`boost::get<T>(boost::optional<T> ...)` overload matches for a non-`boost::optional` type when boost namespace is associated with argument's type of unqualified call to `get<T>`.  
  
```cpp  
template <typename U>  
struct X {};  
  
template <typename T, typename U>  
inline int get(X<U> const&) { return 0; }  
  
namespace boost {  
    struct any_type_in_boost_namespace {};  
}  
  
#include <boost/optional.hpp>  
  
class tag;  
  
int main()  
{  
    get<tag>(X<boost::any_type_in_boost_namespace>{});  
}  
```  
  
https://godbolt.org/z/14d476  
  
The example obviously should compile, but instead it tries to instantiate `boost::optional<tag>` and fails the compilation.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2021-02-28 23:38:43 UTC  
> Url: https://github.com/boostorg/optional/issues/90#issuecomment-787546816  

Thanks for reporting this. I should be able to look at it in a couple of days.
