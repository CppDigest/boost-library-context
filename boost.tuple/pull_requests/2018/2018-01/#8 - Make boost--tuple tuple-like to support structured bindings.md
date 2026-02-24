# #8 Make boost::tuple tuple-like to support structured bindings [Merged]

> Username: pdimov  
> Created at: 2018-01-03 19:00:29 UTC  
> Updated at: 2020-05-26 01:32:51 UTC  
> Merged at: 2018-01-03 23:10:48 UTC  
> Closed at: 2018-01-03 23:10:48 UTC  
> Url: https://github.com/boostorg/tuple/pull/8  



---

## Comment 1

> Username: Kojoley  
> Created_at: 2018-01-04 20:23:37 UTC  
> Url: https://github.com/boostorg/tuple/pull/8#issuecomment-355388897  

Clang 5.0.0 std=c++11/14, libc++ 1.0~svn199600  
```  
In file included from container_support.cpp:15:  
In file included from ../../../../boost/unordered_map.hpp:17:  
In file included from ../../../../boost/unordered/unordered_map.hpp:21:  
In file included from ../../../../boost/unordered/detail/map.hpp:6:  
In file included from ../../../../boost/unordered/detail/implementation.hpp:30:  
../../../../boost/tuple/tuple.hpp:77:5: error: 'tuple_size' defined as a struct template here but previously declared as a class template [-Werror,-Wmismatched-tags]  
    struct tuple_size< boost::tuples::tuple<T1, T2, T3, T4, T5, T6, T7, T8, T9, T10> >:  
    ^  
/usr/include/c++/v1/__tuple:30:22: note: did you mean struct here?  
template <class _Tp> class __attribute__ ((__type_visibility__("default"))) tuple_size;  
                     ^  
In file included from container_support.cpp:15:  
In file included from ../../../../boost/unordered_map.hpp:17:  
In file included from ../../../../boost/unordered/unordered_map.hpp:21:  
In file included from ../../../../boost/unordered/detail/map.hpp:6:  
In file included from ../../../../boost/unordered/detail/implementation.hpp:30:  
../../../../boost/tuple/tuple.hpp:82:28: error: 'tuple_size' defined as a struct template here but previously declared as a class template [-Werror,-Wmismatched-tags]  
template<class H, class T> struct tuple_size< boost::tuples::cons<H, T> >: boost::tuples::length< boost::tuples::cons<H, T> >  
                           ^  
/usr/include/c++/v1/__tuple:30:22: note: did you mean struct here?  
template <class _Tp> class __attribute__ ((__type_visibility__("default"))) tuple_size;  
                     ^  
In file included from container_support.cpp:15:  
In file included from ../../../../boost/unordered_map.hpp:17:  
In file included from ../../../../boost/unordered/unordered_map.hpp:21:  
In file included from ../../../../boost/unordered/detail/map.hpp:6:  
In file included from ../../../../boost/unordered/detail/implementation.hpp:30:  
../../../../boost/tuple/tuple.hpp:86:12: error: 'tuple_size' defined as a struct template here but previously declared as a class template [-Werror,-Wmismatched-tags]  
template<> struct tuple_size< boost::tuples::null_type >: boost::tuples::length< boost::tuples::null_type >  
           ^  
/usr/include/c++/v1/__tuple:30:22: note: did you mean struct here?  
template <class _Tp> class __attribute__ ((__type_visibility__("default"))) tuple_size;  
                     ^  
In file included from container_support.cpp:15:  
In file included from ../../../../boost/unordered_map.hpp:17:  
In file included from ../../../../boost/unordered/unordered_map.hpp:21:  
In file included from ../../../../boost/unordered/detail/map.hpp:6:  
In file included from ../../../../boost/unordered/detail/implementation.hpp:30:  
../../../../boost/tuple/tuple.hpp:93:5: error: 'tuple_element' defined as a struct template here but previously declared as a class template [-Werror,-Wmismatched-tags]  
    struct tuple_element< I, boost::tuples::tuple<T1, T2, T3, T4, T5, T6, T7, T8, T9, T10> >:  
    ^  
/usr/include/c++/v1/__tuple:44:34: note: did you mean struct here?  
template <size_t _Ip, class _Tp> class __attribute__ ((__type_visibility__("default"))) tuple_element;  
                                 ^  
In file included from container_support.cpp:15:  
In file included from ../../../../boost/unordered_map.hpp:17:  
In file included from ../../../../boost/unordered/unordered_map.hpp:21:  
In file included from ../../../../boost/unordered/detail/map.hpp:6:  
In file included from ../../../../boost/unordered/detail/implementation.hpp:30:  
../../../../boost/tuple/tuple.hpp:98:43: error: 'tuple_element' defined as a struct template here but previously declared as a class template [-Werror,-Wmismatched-tags]  
template<std::size_t I, class H, class T> struct tuple_element< I, boost::tuples::cons<H, T> >: boost::tuples::element< I, boost::tuples::cons<H, T> >  
                                          ^  
/usr/include/c++/v1/__tuple:44:34: note: did you mean struct here?  
template <size_t _Ip, class _Tp> class __attribute__ ((__type_visibility__("default"))) tuple_element;  
                                 ^  
5 errors generated.  
```  
  
Boost.Config problem?

---

## Comment 2

> Username: pdimov  
> Created_at: 2018-01-04 21:22:09 UTC  
> Url: https://github.com/boostorg/tuple/pull/8#issuecomment-355402257  

This is impossible to fix. `tuple_size` is sometimes a class, sometimes a struct; even the standard is not consistent. class/struct are synonyms, and the warning is nonstandard.

---

## Comment 3

> Username: cwecht  
> Created_at: 2018-08-20 12:41:16 UTC  
> Url: https://github.com/boostorg/tuple/pull/8#issuecomment-414302983  

To everyone, who sticks to an older version of boost (like 1.65 which ships with Ubuntu 18.04): you can use [this](https://gist.github.com/cwecht/ad794c1bebff16a315a626d0cdcb2421). It is basically this PR moved into another file to make it usable with older versions of boost without changing the boost-source-code.

---

## Comment 4

> Username: lanyizi  
> Created_at: 2020-05-26 00:48:05 UTC  
> Url: https://github.com/boostorg/tuple/pull/8#issuecomment-633755265  

Is there any plan to merge this commit into the master branch?  
Currently (as for Boost 1.73), boost's tuples still don't support structured binding, because this commit never ended up into any Boost release...

---

## Comment 5

> Username: djowel  
> Created_at: 2020-05-26 01:32:50 UTC  
> Url: https://github.com/boostorg/tuple/pull/8#issuecomment-633763773  

Merged to master.

---
