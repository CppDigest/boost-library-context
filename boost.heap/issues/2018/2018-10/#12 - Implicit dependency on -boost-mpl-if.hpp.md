# #12 - Implicit dependency on <boost/mpl/if.hpp>? [Closed]

> Username: jbeich  
> Created at: 2018-10-22 21:38:00 UTC  
> Updated at: 2018-10-29 08:36:55 UTC  
> Closed at: 2018-10-29 08:36:55 UTC  
> Url: https://github.com/boostorg/heap/issues/12  

After https://github.com/boostorg/concept_check/pull/14 some consumers no longer build: [asc](https://ptpb.pw/UGQX), [colmap](https://ptpb.pw/vw16), [krita](https://ptpb.pw/So9B), [osrm-backend](https://ptpb.pw/9V2R). What's the path forward? CC @mjcaisse  
  
```c++  
#include <boost/heap/fibonacci_heap.hpp>  
  
int main(void)  
{  
  return 0;  
}  
```  
```c++  
$ clang++70 test.cc  
In file included from test.cc:1:  
In file included from /usr/include/boost/heap/fibonacci_heap.hpp:19:  
/usr/include/boost/heap/detail/heap_comparison.hpp:139:34: error: no type named 'if_c' in namespace  
      'boost::mpl'  
    typedef typename boost::mpl::if_c<use_ordered_iterators,  
            ~~~~~~~~~~~~~~~~~~~~~^~~~  
/usr/include/boost/heap/detail/heap_comparison.hpp:139:38: error: expected unqualified-id  
    typedef typename boost::mpl::if_c<use_ordered_iterators,  
                                     ^  
/usr/include/boost/heap/detail/heap_comparison.hpp:144:5: error: unknown type name 'equivalence_check'  
    equivalence_check eq_check;  
    ^  
/usr/include/boost/heap/detail/heap_comparison.hpp:228:34: error: no type named 'if_c' in namespace  
      'boost::mpl'  
    typedef typename boost::mpl::if_c<use_ordered_iterators,  
            ~~~~~~~~~~~~~~~~~~~~~^~~~  
/usr/include/boost/heap/detail/heap_comparison.hpp:228:38: error: expected unqualified-id  
    typedef typename boost::mpl::if_c<use_ordered_iterators,  
                                     ^  
/usr/include/boost/heap/detail/heap_comparison.hpp:233:5: error: unknown type name 'compare_check'  
    compare_check check_object;  
    ^  
6 errors generated.  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2018-10-28 16:52:33 UTC  
> Url: https://github.com/boostorg/heap/issues/12#issuecomment-433722036  

cc @glenfe :-)

---

## Comment 2

> Username: pdimov  
> Created at: 2018-10-28 16:54:58 UTC  
> Url: https://github.com/boostorg/heap/issues/12#issuecomment-433722204  

@jbeich When you already have a failing test, it's better to use a pull request that adds it, instead of an issue. Not that an issue doesn't work and we don't appreciate it all the same, of course. :-)

---

## Comment 3

> Username: Lastique  
> Created at: 2018-10-28 17:01:24 UTC  
> Url: https://github.com/boostorg/heap/issues/12#issuecomment-433722666  

I'll create a PR for Boost.Heap adding the missing includes.

---

## Comment 4

> Username: Lastique  
> Created at: 2018-10-28 20:15:33 UTC  
> Url: https://github.com/boostorg/heap/issues/12#issuecomment-433737592  

See https://github.com/boostorg/heap/pull/13.

---

## Comment 5

> Username: glenfe  
> Created at: 2018-10-28 21:09:05 UTC  
> Url: https://github.com/boostorg/heap/issues/12#issuecomment-433741613  

Also #14.

---

## Comment 6

> Username: pdimov  
> Created at: 2018-10-29 05:07:54 UTC  
> Url: https://github.com/boostorg/heap/issues/12#issuecomment-433789722  

I enabled Travis; closing and reopening the PRs will trigger it.

---

## Comment 7

> Username: timblechmann  
> Created at: 2018-10-29 07:35:53 UTC  
> Url: https://github.com/boostorg/heap/issues/12#issuecomment-433813636  

thanks a lot for the PRs! i'd merge #14 to avoid the mpl dependency here
