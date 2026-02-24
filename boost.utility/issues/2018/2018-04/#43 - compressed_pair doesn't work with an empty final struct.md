# #43 - compressed_pair doesn't work with an empty final struct [Closed]

> Username: danieljames  
> Created at: 2018-04-06 08:48:29 UTC  
> Updated at: 2018-09-14 16:54:18 UTC  
> Closed at: 2018-09-13 02:29:46 UTC  
> Url: https://github.com/boostorg/utility/issues/43  

As recently discussed on the user list, final classes break EBO. This test case causes a compile error:  
  
    #include <boost/compressed_pair.hpp>  
  
    struct final_type final {};  
  
    int main() {  
        boost::compressed_pair<final_type, int> x;  
    }  
  
This can be fixed for some compilers using a wrapper such as this one by Nevin Liber: https://github.com/nliber/cool/blob/master/ebo_wrapper.h using `boost::is_final` rather than `std::is_final`. Such a wrapper would also be useful for other libraries which implement EBO themselves.  
  
`boost::is_final` from type traits supports recent clang and gcc, and will hopefully support recent Visual C++ 2013 and later with boostorg/type_traits#60. That will still leave some compilers where final is available but `boost::is_final` doesn't work. As Nevin suggested, the user can specialize `is_final` to support such types. I don't see any better solution for such compilers.

---

## Comment 1

> Username: danieljames  
> Created at: 2018-04-06 09:06:06 UTC  
> Url: https://github.com/boostorg/utility/issues/43#issuecomment-379194140  

Looking at the implementation, rather than using a wrapper class, it might be better to change the instantiation of `compressed_pair_switch` to check both `is_empty` and `is_final`. I think we can just use: `::boost::is_empty<T1>::value && !::boost::is_final<T1>::value`, I assume we don't have to worry about old compilers with poor support for integral constant expressions anymore.

---

## Comment 2

> Username: Lastique  
> Created at: 2018-04-06 09:42:15 UTC  
> Url: https://github.com/boostorg/utility/issues/43#issuecomment-379203347  

I would suggest testing for `BOOST_IS_FINAL` before using `boost::is_final`. Otherwise it will break `compressed_pair` for compilers that don't support final classes.  
  
In fact, it might make sense to detect if `std::is_final` is available and only use it in `compressed_pair` when it is available. This way we're not depending on Boost.TypeTraits implementation details, which `BOOST_IS_FINAL` is.

---

## Comment 3

> Username: danieljames  
> Created at: 2018-04-06 09:50:35 UTC  
> Url: https://github.com/boostorg/utility/issues/43#issuecomment-379205477  

`boost::is_final` returns `false` when it isn't supported, so it will always do the right thing for compilers that don't support final classes.  
  
Using `std::is_final` could be good, but we currently don't have a mechanism for detecting it, so it's unlikely we'll get better support than `boost::is_final` without a fair bit of work, e.g. libstdc++ doesn't have `std::is_final` in C++11 mode, but does have `boost::is_final`.  
  
Utility already makes heavy use of Type Traits, so we won't be adding another dependency.

---

## Comment 4

> Username: Lastique  
> Created at: 2018-04-06 10:14:09 UTC  
> Url: https://github.com/boostorg/utility/issues/43#issuecomment-379211092  

> `boost::is_final` returns `false` when it isn't supported, so it will always do the right thing for compilers that don't support final classes.  
  
Right, sorry, I didn't think it through enough.

---

## Comment 5

> Username: danieljames  
> Created at: 2018-04-06 10:17:01 UTC  
> Url: https://github.com/boostorg/utility/issues/43#issuecomment-379211704  

I've been trying to implement this for unordered, and it turns out that `boost::is_final` doesn't work well with templates on gcc, I created an issue at boostorg/type_traits#66. So if it can't be fixed in Type Traits, it might be best to use `std::is_final` when available after all.

---

## Comment 6

> Username: danieljames  
> Created at: 2018-04-06 10:26:10 UTC  
> Url: https://github.com/boostorg/utility/issues/43#issuecomment-379213669  

It looks like `boost::is_empty` doesn't work with final classes either.

---

## Comment 7

> Username: Lastique  
> Created at: 2018-04-06 12:37:28 UTC  
> Url: https://github.com/boostorg/utility/issues/43#issuecomment-379240263  

Looks like `boost::is_empty` is implemented as emulation even if a compiler intrinsic is available. @jzmaddock is there a reason for this?

---

## Comment 8

> Username: glenfe  
> Created at: 2018-09-10 14:46:54 UTC  
> Url: https://github.com/boostorg/utility/issues/43#issuecomment-419940004  

@danieljames We now also have `boost::empty_value` which is a slightly more flexible version of `ebo_helper`:  
* https://www.boost.org/doc/libs/master/libs/core/doc/html/core/empty_value.html  
* https://github.com/boostorg/core/blob/master/include/boost/core/empty_value.hpp  
  
e.g. 1  
```  
template<class Allocator>  
class type  
    : empty_value<Allocator> {  
```  
e.g. 2  
```  
template<class InnerAllocator, class OuterAllocator, class OtherAllocator>  
class type  
    : empty_value<InnerAllocator, 1>  
    , empty_value<OuterAllocator, 2>  
    , empty_value<OtherAllocator, 3> {  
```  
That index is optional, and can be specified to create distinct base types so that the optimization still kicks in when all three Allocators are the same type.

---

## Comment 9

> Username: pdimov  
> Created at: 2018-09-11 13:47:44 UTC  
> Url: https://github.com/boostorg/utility/issues/43#issuecomment-420280656  

Wouldn't `compressed_pair` be easier as `template<class T1, class T2> class compressed_pair: empty_value<T1, 1>, empty_value<T2, 2>`?

---

## Comment 10

> Username: pdimov  
> Created at: 2018-09-11 13:50:50 UTC  
> Updated at: 2018-09-11 13:51:19 UTC  
> Url: https://github.com/boostorg/utility/issues/43#issuecomment-420281620  

Also `template<class... T> compressed_tuple: empty_value<T, i>... {}` with the appropriate `index_sequence` magic applied. :-)

---

## Comment 11

> Username: Lastique  
> Created at: 2018-09-11 13:59:51 UTC  
> Url: https://github.com/boostorg/utility/issues/43#issuecomment-420284586  

I think, the regular `boost::tuple` can implement compression, if it doesn't yet. `std::tuple` does.

---

## Comment 12

> Username: pdimov  
> Created at: 2018-09-11 14:06:36 UTC  
> Url: https://github.com/boostorg/utility/issues/43#issuecomment-420286868  

Some std:tuples do, some don't, I think?  
  
boost::tuple is legacy C++03 and I don't think we can change it because I suspect much code relies on its internals. Although who knows.

---

## Comment 13

> Username: Lastique  
> Created at: 2018-09-11 14:09:56 UTC  
> Url: https://github.com/boostorg/utility/issues/43#issuecomment-420287961  

Even if someone depends on its implementation details, it shouldn't stop us from developing the library.

---

## Comment 14

> Username: glenfe  
> Created at: 2018-09-11 14:10:45 UTC  
> Url: https://github.com/boostorg/utility/issues/43#issuecomment-420288232  

> Wouldn't `compressed_pair` be easier as `template<class T1, class T2> class compressed_pair: empty_value<T1, 1>, empty_value<T2, 2>`?  
  
Yes.

---

## Comment 15

> Username: pdimov  
> Created at: 2018-09-11 14:25:04 UTC  
> Url: https://github.com/boostorg/utility/issues/43#issuecomment-420292948  

They are documented: https://www.boost.org/doc/libs/1_68_0/libs/tuple/doc/html/tuple_advanced_interface.html  
  
So, not implementation details.  
  
And you pretty much have to rely on them, I think, because if I recall correctly tuple<> is not the same type as tuple<>::inherited (which is what make_tuple returns.) So if you want to write a function that accepts tuples you need to handle both.  
  
All that said, std::tuple is very slow to instantiate for reasons I always forget, so it'd probably be useful to have a Boost tuple that isn't. I'm just not sure whether it can be called boost::tuple.

---

## Comment 16

> Username: Lastique  
> Created at: 2018-09-11 14:30:05 UTC  
> Url: https://github.com/boostorg/utility/issues/43#issuecomment-420294635  

I still think we should try and improve `boost::tuple`, even if it involves deprecating or changing some things. Tuples are supposed to be vocabulary types, and we already have a few implementations in different libraries. Let's not make another one.

---

## Comment 17

> Username: pdimov  
> Created at: 2018-09-11 14:42:44 UTC  
> Url: https://github.com/boostorg/utility/issues/43#issuecomment-420299026  

We have one in Hana. Tuples aren't exactly vocabulary types though. std::tuple is, the rest are tuple-likes.  
  
Never mind. :-)

---

## Comment 18

> Username: jzmaddock  
> Created at: 2018-09-14 16:22:40 UTC  
> Url: https://github.com/boostorg/utility/issues/43#issuecomment-421410964  

Sorry... been away and just come back to this - do we still need to fix compressed_pair?

---

## Comment 19

> Username: glenfe  
> Created at: 2018-09-14 16:45:08 UTC  
> Updated at: 2018-09-14 16:54:18 UTC  
> Url: https://github.com/boostorg/utility/issues/43#issuecomment-421417305  

I fixed it in #49 . We can improve (simplify) compressed_pair, but that can happen later.
