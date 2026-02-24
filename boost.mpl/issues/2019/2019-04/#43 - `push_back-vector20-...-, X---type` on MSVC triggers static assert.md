# #43 - `push_back<vector20<...>, X>::type` on MSVC triggers static assert [Open]

> Username: Kojoley  
> Created at: 2019-04-29 13:21:54 UTC  
> Updated at: 2022-05-01 21:27:28 UTC  
> Url: https://github.com/boostorg/mpl/issues/43  

The following code is fine on GCC and Clang, but fails on MSVC and ICC.  
```cpp  
#include <boost/mpl/vector.hpp>  
#include <boost/mpl/push_back.hpp>  
  
struct X;  
  
using vec = boost::mpl::vector<X, X, X, X, X, X, X, X, X, X, X, X, X, X, X, X, X, X, X, X>;  
using test = boost::mpl::push_back<vec, X>::type;  
```  
  
https://godbolt.org/z/w-yTto  
  
This inconsistency confuses people: 1) https://stackoverflow.com/questions/55900932/issue-with-x3-and-ms-vs2017 2) https://github.com/boostorg/spirit/issues/515

---

## Comment 1

> Username: jeking3  
> Created at: 2022-05-01 21:27:28 UTC  
> Url: https://github.com/boostorg/mpl/issues/43#issuecomment-1114340970  

@Kojoley could you add a test for this?  CI will run it against a lot of different platforms now.
