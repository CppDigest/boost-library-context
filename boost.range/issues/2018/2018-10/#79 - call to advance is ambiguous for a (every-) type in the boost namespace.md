# #79 - call to advance is ambiguous for a (every?) type in the boost namespace? [Closed]

> Username: dvd0101  
> Created at: 2018-10-26 21:21:50 UTC  
> Updated at: 2018-10-30 14:38:46 UTC  
> Closed at: 2018-10-30 14:38:46 UTC  
> Url: https://github.com/boostorg/range/issues/79  

This code  
  
```c++  
#include <boost/smart_ptr/shared_ptr.hpp>  
#include <boost/range.hpp>  
#include <vector>  
  
void f(std::vector<boost::shared_ptr<int>>& c) {  
    using std::advance;  
    auto it = c.begin();  
    advance(it, 1);  
}  
```  
  
fails to compile with boost 1.68 on both GCC and Clang with the same error, the call to advance is ambiguos:  
  
> <source>: In function 'void f(std::vector<boost::shared_ptr<int> >&)':  
>   
> <source>:8:18: error: call of overloaded 'advance(__gnu_cxx::__normal_iterator<boost::shared_ptr<int>*, std::vector<boost::shared_ptr<int> > >&, int)' is ambiguous  
>   
> /opt/compiler-explorer/gcc-8.2.0/include/c++/8.2.0/bits/stl_iterator_base_funcs.h:202:5: note: candidate: 'constexpr void std::advance(_InputIterator&, _Distance) [with _InputIterator = __gnu_cxx::__normal_iterator<boost::shared_ptr<int>*, std::vector<boost::shared_ptr<int> > >; _Distance = int]'  
>   
> /opt/compiler-explorer/libs/boost_1_65_0/boost/iterator/advance.hpp:68:9: note: candidate: 'constexpr void boost::iterators::advance_adl_barrier::advance(InputIterator&, Distance) [with InputIterator = __gnu_cxx::__normal_iterator<boost::shared_ptr<int>*, std::vector<boost::shared_ptr<int> > >; Distance = int]'  
  
I've reduced the code even further:  
  
```c++  
#include <boost/range.hpp>  
#include <vector>  
  
namespace boost {  
    class test {};  
}  
  
void f(std::vector<boost::test>& c) {  
    using std::advance;  
    auto it = c.begin();  
    advance(it, 1);  
}  
```  
  
even this code fails to compile.  
  
This snippet is live on godbolt: https://godbolt.org/z/fAlTh8  
  
Interesting the error appears for the first time on boost 1.65; thanks to godbolt it's possible to quickly verify (use the "Libraries" combobox on the right pane) that with boost 1.64 the code compiles fine.

---

## Comment 1

> Username: dvd0101  
> Created at: 2018-10-30 14:38:46 UTC  
> Url: https://github.com/boostorg/range/issues/79#issuecomment-434327102  

I've reduced the code snippet even further to:  
  
```c++  
#include <boost/iterator/advance.hpp>  
#include <vector>  
  
namespace boost {  
        class test {};  
}  
  
void f(std::vector<boost::test>& c) {  
        using std::advance;  
        auto it = c.begin();  
        advance(it, 1);  
}  
```  
  
I've opened a new issue to boost::iterator (https://github.com/boostorg/iterator/issues/45) since boost::range is not involved.
