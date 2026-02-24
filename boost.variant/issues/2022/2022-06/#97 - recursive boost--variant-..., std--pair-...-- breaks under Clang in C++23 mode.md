# #97 - recursive boost::variant<..., std::pair<...>> breaks under Clang in C++23 mode [Closed]

> Username: ecatmur  
> Created at: 2022-06-10 10:16:19 UTC  
> Updated at: 2022-09-06 19:57:35 UTC  
> Closed at: 2022-09-06 19:57:35 UTC  
> Url: https://github.com/boostorg/variant/issues/97  

```c++  
#include <boost/spirit/home/support/info.hpp>  
```  
  
or, reduced,  
```c++  
#include <boost/variant/variant.hpp>  
#include <boost/variant/recursive_wrapper.hpp>  
#include <string_view>  
#include <utility>  
  
struct B {  
    boost::variant<  
        std::string_view  
        , boost::recursive_wrapper<std::pair<B, int>>> value;  
};  
```  
  
when compiled under clang 13/14/15 in C++23 mode (-std=c++2b) this fails with:  
```  
include/c++/13.0.0/bits/stl_pair.h:193:11: error: field has incomplete type 'B'  
      _T1 first;                 ///< The first member  
          ^  
[...]  
include/c++/13.0.0/bits/ranges_base.h:585:2: note: while checking constraint satisfaction for template 'operator()<boost::recursive_wrapper<std::pair<B, int>> &>' required here  
        ranges::begin(__t);  
        ^~~~~~  
[...]  
include/c++/13.0.0/string_view:156:7: note: while substituting template arguments into constraint expression here  
          && ranges::contiguous_range<_Range>  
             ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/type_traits/is_constructible.hpp:37:73: note: while checking constraint satisfaction for template 'basic_string_view<boost::recursive_wrapper<std::pair<B, int>>, boost::recursive_wrapper<std::pair<B, int>>>' required here  
         template<typename T, typename Arg, typename = decltype(::new T(boost::declval<Arg>()))>  
                                                                        ^~~~~  
[...]  
boost/type_traits/is_constructible.hpp:37:73: note: in instantiation of function template specialization 'std::basic_string_view<char>::basic_string_view<boost::recursive_wrapper<std::pair<B, int>>, boost::recursive_wrapper<std::pair<B, int>>>' requested here  
[...]  
boost/mpl/aux_/nested_type_wknd.hpp:27:7: note: in instantiation of template class 'boost::is_constructible<std::basic_string_view<char>, boost::recursive_wrapper<std::pair<B, int>>>' requested here  
    : T::type  
      ^  
[...]  
boost/variant/variant.hpp:1105:7: note: while substituting deduced template arguments into function template 'operator=' [with T = boost::variant<std::basic_string_view<char>, boost::recursive_wrapper<std::pair<B, int>>>]  
class variant  
      ^  
<source>:9:8: note: while declaring the implicit move assignment operator for 'B'  
struct B {  
       ^  
<source>:9:8: note: definition of 'B' is not complete until the closing '}'  
```

---

## Comment 1

> Username: ecatmur  
> Created at: 2022-06-10 10:17:39 UTC  
> Url: https://github.com/boostorg/variant/issues/97#issuecomment-1152208369  

Possible fix:  
```c++  
#include <boost/variant/recursive_wrapper.hpp>  
template<class T, class U> struct boost::is_constructible<U, boost::recursive_wrapper<T>> : boost::false_type{};  
template<class T, class U> struct boost::is_constructible<U, boost::recursive_wrapper<T>&> : boost::false_type{};  
```

---

## Comment 2

> Username: ecatmur  
> Created at: 2022-06-29 10:58:56 UTC  
> Url: https://github.com/boostorg/variant/issues/97#issuecomment-1169839542  

Ah, it doesn't have to be `recursive_wrapper`:  
  
```c++  
#include <boost/variant/variant.hpp>  
#include <list>  
#include <string_view>  
#include <utility>  
struct V {  
    boost::variant<  
        std::string_view,  
        std::list<std::pair<V, int>>> v;  
};  
```

---

## Comment 3

> Username: apolukhin  
> Created at: 2022-08-31 18:39:53 UTC  
> Url: https://github.com/boostorg/variant/issues/97#issuecomment-1233285869  

Could you please confirm, that it is fixed in #98 ?

---

## Comment 4

> Username: ecatmur  
> Created at: 2022-09-06 19:57:35 UTC  
> Url: https://github.com/boostorg/variant/issues/97#issuecomment-1238590932  

Fixed by #98.
