# #45 - call to advance is ambiguous for a (every?) type in the boost namespace? [Closed]

> Username: dvd0101  
> Created at: 2018-10-30 14:36:53 UTC  
> Updated at: 2018-10-30 15:21:39 UTC  
> Closed at: 2018-10-30 15:21:39 UTC  
> Url: https://github.com/boostorg/iterator/issues/45  

This code  
  
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
  
fails to compile since boost 1.65 on both GCC and Clang with the same error, the call to advance is ambiguous:  
  
> <source>: In function 'void f(std::vector<boost::shared_ptr<int> >&)':  
>   
> <source>:8:18: error: call of overloaded 'advance(__gnu_cxx::__normal_iterator<boost::shared_ptr<int>*, std::vector<boost::shared_ptr<int> > >&, int)' is ambiguous  
>   
> /opt/compiler-explorer/gcc-8.2.0/include/c++/8.2.0/bits/stl_iterator_base_funcs.h:202:5: note: candidate: 'constexpr void std::advance(_InputIterator&, _Distance) [with _InputIterator = __gnu_cxx::__normal_iterator<boost::shared_ptr<int>*, std::vector<boost::shared_ptr<int> > >; _Distance = int]'  
>   
> /opt/compiler-explorer/libs/boost_1_65_0/boost/iterator/advance.hpp:68:9: note: candidate: 'constexpr void boost::iterators::advance_adl_barrier::advance(InputIterator&, Distance) [with InputIterator = __gnu_cxx::__normal_iterator<boost::shared_ptr<int>*, std::vector<boost::shared_ptr<int> > >; Distance = int]'  
  
The include `<boost/iterator/advance.hpp>` was introduced with boost 1.65 among some tests that, unluckily, never tests an unqualified call to advance.

---

## Comment 1

> Username: Lastique  
> Created at: 2018-10-30 15:21:39 UTC  
> Url: https://github.com/boostorg/iterator/issues/45#issuecomment-434344489  

Duplicates https://github.com/boostorg/iterator/issues/43.
