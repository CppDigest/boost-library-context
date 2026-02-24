# #80 - flat_map deduction guides are ambiguous [Closed]

> Username: gix  
> Created at: 2018-08-15 15:04:30 UTC  
> Updated at: 2018-12-31 17:25:14 UTC  
> Closed at: 2018-12-31 17:25:14 UTC  
> Url: https://github.com/boostorg/container/issues/80  

The `flat_map` and `flat_multimap` deduction guides are ambiguous. More specifically, the `(InputIterator, InputIterator, Allocator const&)` and `(InputIterator, InputIterator, Compare const&)` guides clash.  
  
Repro:  
  
    #include <boost/container/flat_map.hpp>  
    #include <functional>  
  
    void f()  
    {  
        std::pair<int, bool> v;  
        boost::container::flat_map m(&v, &v + 1, std::greater<int>());  
    }  
  
Clang 8:  
  
    test.cpp(7,32):  error: ambiguous deduction for template arguments of 'flat_map'  
        boost::container::flat_map m(&v, &v + 1, std::greater<int>());  
                                   ^  
    .../boost/container/flat_map.hpp(1556,1):  note: candidate function [with InputIterator = std::pair<int, bool> *, Allocator = std::greater<int>] flat_map(InputIterator, InputIterator, Allocator const&) ->  
    ^  
    .../boost/container/flat_map.hpp(1563,1):  note: candidate function [with InputIterator = std::pair<int, bool> *, Compare = std::greater<int>]  
    flat_map(InputIterator, InputIterator, Compare const&) ->  
    ^  
    1 error generated.  
  
MSVC 19.15.26726:  
  
    D:\> cl -std:c++latest -Zc:__cplusplus -c test.cpp  
    test.cpp  
    .../boost/container/flat_map.hpp(1563): error C2642: two deduction guide declarations for the same class template cannot have equivalent parameter list  
    .../boost/container/flat_map.hpp(1556): note: see previous definition of 'flat_map'  
    .../boost/container/flat_map.hpp(1588): error C2642: two deduction guide declarations for the same class template cannot have equivalent parameter list  
    .../boost/container/flat_map.hpp(1581): note: see previous definition of 'flat_map'  
    .../boost/container/flat_map.hpp(2828): error C2642: two deduction guide declarations for the same class template cannot have equivalent parameter list  
    .../boost/container/flat_map.hpp(2821): note: see previous definition of 'flat_multimap'  
    .../boost/container/flat_map.hpp(2853): error C2642: two deduction guide declarations for the same class template cannot have equivalent parameter list  
    .../boost/container/flat_map.hpp(2846): note: see previous definition of 'flat_multimap'  
  
GCC:  
  
    <source>: In function 'void f()':  
    <source>:7:73: error: class template argument deduction failed:  
                 boost::container::flat_map m(&v, &v + 1, std::greater<int>());  
                                                                             ^  
    <source>:7:73: error: call of overloaded 'flat_map(std::pair<int, bool>*, std::pair<int, bool>*, std::greater<int>)' is ambiguous  
    In file included from <source>:1:  
    .../boost/container/flat_map.hpp:1556:1: note: candidate: 'boost::container::flat_map(InputIterator, InputIterator, const Allocator&)-> boost::container::flat_map<typename boost::move_detail::remove_const<typename boost::movelib::iterator_traits<I>::value_type::first_type>::type, typename boost::movelib::iterator_traits<I>::value_type::second_type, std::less<typename boost::move_detail::remove_const<typename boost::movelib::iterator_traits<I>::value_type::first_type>::type>, Allocator> [with InputIterator = std::pair<int, bool>*; Allocator = std::greater<int>; typename boost::move_detail::remove_const<typename boost::movelib::iterator_traits<I>::value_type::first_type>::type = int; typename boost::movelib::iterator_traits<I>::value_type::second_type = bool]'  
     flat_map(InputIterator, InputIterator, Allocator const&) ->  
     ^~~~~~~~  
    .../boost/container/flat_map.hpp:1563:1: note: candidate: 'boost::container::flat_map(InputIterator, InputIterator, const Compare&)-> boost::container::flat_map<typename boost::move_detail::remove_const<typename boost::movelib::iterator_traits<I>::value_type::first_type>::type, typename boost::movelib::iterator_traits<I>::value_type::second_type, Compare> [with InputIterator = std::pair<int, bool>*; Compare = std::greater<int>; typename boost::movelib::iterator_traits<I>::value_type::second_type = bool; typename boost::move_detail::remove_const<typename boost::movelib::iterator_traits<I>::value_type::first_type>::type = int]'  
     flat_map(InputIterator, InputIterator, Compare const&) ->  
     ^~~~~~~~  
    Compiler returned: 1

---

## Comment 1

> Username: fxb  
> Created at: 2018-10-16 21:32:30 UTC  
> Url: https://github.com/boostorg/container/issues/80#issuecomment-430408686  

We are currently trying to make our codebase compile as C++17 and this issue appeared. I would be happy to see a fix as well, but will for now manually patch deduction guides away.

---

## Comment 2

> Username: fxb  
> Created at: 2018-10-18 14:33:34 UTC  
> Url: https://github.com/boostorg/container/issues/80#issuecomment-431032692  

The STL solves issues like this by having [`std::allocator_arg`](https://en.cppreference.com/w/cpp/memory/allocator_arg) for disambiguation, but introducing something like that would be a breaking API change in Boost.Container.  
  
Here is a solution which disables a non-matching deduction guide using SFINAE:  
  
These specialized `enable_if`'s could be added to `<boost/container/detail/type_traits.hpp>`:  
  
```  
namespace dtl {  
  
template<class T, class U>  
using enable_if_allocator_t = std::enable_if_t<std::is_same_v<decltype(std::declval<T>().allocate(0)), U *>, T>;  
  
template<class T, class U>  
using enable_if_compare_t = std::enable_if_t<std::is_same_v<decltype(std::declval<T>()(std::declval<U>(), std::declval<U>())), bool>, T>;  
  
}  // namespace dtl  
```  
  
Then deduction guides can be changed to use them:  
  
```  
template <typename InputIterator, typename Allocator>  
flat_map(InputIterator, InputIterator, Allocator const&) ->  
   flat_map< typename dtl::remove_const< typename iterator_traits<InputIterator>::value_type::first_type>::type  
           , typename iterator_traits<InputIterator>::value_type::second_type  
           , std::less<typename dtl::remove_const<typename iterator_traits<InputIterator>::value_type::first_type>::type>  
           , dtl::enable_if_allocator_t<Allocator, std::remove_const_t<typename iterator_traits<InputIterator>::value_type>>>;  
  
template <typename InputIterator, typename Compare>  
flat_map(InputIterator, InputIterator, Compare const&) ->  
   flat_map< typename dtl::remove_const<typename iterator_traits<InputIterator>::value_type::first_type>::type  
           , typename iterator_traits<InputIterator>::value_type::second_type  
           , dtl::enable_if_compare_t<Compare, std::remove_const_t<typename iterator_traits<InputIterator>::value_type::first_type>>>;  
  
```  
See a working example here: https://godbolt.org/z/7DrMkB

---

## Comment 3

> Username: tobias-loew  
> Created at: 2018-11-02 11:18:58 UTC  
> Updated at: 2018-11-02 11:26:56 UTC  
> Url: https://github.com/boostorg/container/issues/80#issuecomment-435347809  

The problem is the C++17 standard (and also the current C++20 draft). In [temp.deduct.guide] (17.9.3, C++17) it says:  
  
> Two deduction guide declarations in the same translation unit for the same class template shall  
not have equivalent parameter-declaration-clauses  
  
So, the standard currently doesn't allow SFINAE to disambiguate CTADs.  
Apparently, gcc has an implementation supporting SFINAE for CTADs. Maybe someone could write a proposal for the standard?

---

## Comment 4

> Username: igaztanaga  
> Created at: 2018-11-12 20:41:41 UTC  
> Url: https://github.com/boostorg/container/issues/80#issuecomment-438021606  

I don't think the standard has any problem. The standard (C++17) says:  
  
*********  
  
26.2.6 Associative containers  
  
15 A deduction guide for an associative container shall not participate in overload resolution if any of the  
following are true:  
(15.1) — It has an InputIterator template parameter and a type that does not qualify as an input iterator is deduced for that parameter.  
(15.2) — It has an Allocator template parameter and a type that does not qualify as an allocator is deduced for that parameter.  
(15.3) — It has a Compare template parameter and a type that qualifies as an allocator is deduced for that parameter.  
  
*********  
  
so the current implementation is wrong and deduction guidelines shall be sfinae-d out on those parameters (InputIterator is not strictly necessary to avoid the ambiguous overload). Some experiments show that GCC 7 and Clang 6 support SFINAE on CTAD, but not MSVC (in c++17 mode). So it seems that the standard allows SFINAE but MSVC does not support it yet.   
  
I'll at least SFINAE out Compare and Allocator parameters in [flat_]map/set CTADs to disambiguate them.

---

## Comment 5

> Username: igaztanaga  
> Created at: 2018-11-12 22:00:54 UTC  
> Url: https://github.com/boostorg/container/issues/80#issuecomment-438043512  

Further elaborated the proposed patch (thanks!) and merged in commit:  
  
https://github.com/boostorg/container/commit/742b47fe29e42d9e813d341e73763a87e8d684c8  
  
Conforming compilers should now disambiguate CTAD for tree and flat associative containers.

---

## Comment 6

> Username: tobias-loew  
> Created at: 2018-11-13 08:38:59 UTC  
> Url: https://github.com/boostorg/container/issues/80#issuecomment-438181638  

Thanks for pointing out the CTAD specialization for containers. I've reported this to the VC++ team.: https://developercommunity.visualstudio.com/content/problem/381294/ctad-overloads-and-sfinae.html

---

## Comment 7

> Username: igaztanaga  
> Created at: 2018-12-01 11:49:52 UTC  
> Url: https://github.com/boostorg/container/issues/80#issuecomment-443420667  

Let's see if after commit:  
  
https://github.com/boostorg/container/commit/51669a5330a4d9fad91559fbae8ba63c910c6ce2  
  
MSVC support is also included.

---

## Comment 8

> Username: tobias-loew  
> Created at: 2018-12-01 13:11:54 UTC  
> Url: https://github.com/boostorg/container/issues/80#issuecomment-443425247  

It should. That commit was by me and I wrote it on visual studio.

---

## Comment 9

> Username: igaztanaga  
> Created at: 2018-12-31 17:25:14 UTC  
> Url: https://github.com/boostorg/container/issues/80#issuecomment-450671588  

Closing the issue. Many thanks!
