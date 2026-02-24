# #67 Try to include std::swap from the most lightweight header [Merged]

> Username: Kojoley  
> Created at: 2019-12-01 15:22:02 UTC  
> Updated at: 2019-12-05 00:01:45 UTC  
> Merged at: 2019-12-02 05:29:03 UTC  
> Closed at: 2019-12-02 05:29:03 UTC  
> Url: https://github.com/boostorg/core/pull/67  

Based on Boost.Move idea

---

## Comment 1

> Username: pdimov  
> Created_at: 2019-12-01 16:17:03 UTC  
> Updated_at: 2019-12-01 16:17:41 UTC  
> Url: https://github.com/boostorg/core/pull/67#issuecomment-560126526  

I don't mind including either `<utility>` or `<algorithm>` depending on some criteria, but the private headers in bits/ don't sit well with me, and neither does the nonstandard assumption that swap is in `<type_traits>` for libc++.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2019-12-01 17:13:44 UTC  
> Url: https://github.com/boostorg/core/pull/67#issuecomment-560132796  

> but the private headers in bits/ don't sit well with me, and neither does the nonstandard assumption that swap is in <type_traits> for libc++.  
  
It worked for [Boost.Move](https://github.com/boostorg/move/commit/cbda10ecdde552655a664cdbbd9af9e7ea55b1c2) for 5 years already without any changes. Will you be more comfortable if this would be done by Boost.Config?  
  
Some values for consideration:  
  
compiler            | cur  |  11  | this  
--------------------|------|------|-----  
gcc 8.3             | 0.10 | 0.05 | 0.03  
clang 9             | 0.15 | 0.06 | 0.05  
clang 9 libc++      | 0.42 | 0.18 | 0.13  
vc14.2              | 0.14 | 0.14 | 0.06  
clang-cl 9 (vc14.2) | 0.22 | 0.10 | 0.09  
mingw (gcc 9.2)     | 0.12 | 0.04 | 0.03  
  
C++11 column is just:  
```cpp  
#if __cplusplus >= 201103L  
# include <utility>  
#else // C++98/03 fallback  
# include <algorithm>  
#endif  
```  
  
Times for current and C++11 include are same for MSVC because it defines __cplusplus with a 1997 value.  
  
Invoke commands:  
```bash  
echo '#include "boost/core/swap.hpp"' | g++/clang++ -fsyntax-only -x c++ -I. -ftime-report -  
cl /d1reportTime time-include.cpp -I.  
```

---

## Comment 3

> Username: pdimov  
> Created_at: 2019-12-01 17:33:29 UTC  
> Url: https://github.com/boostorg/core/pull/67#issuecomment-560134854  

I don't think `-fsyntax-only` is relevant in practice.  
  
I'm not against this because it doesn't work - it obviously does - but because it relies on undocumented implementation details. This is similar to how I avoid relying on undefined behavior that works.  
  
I don't mind checking BOOST_MSVC in addition to __cplusplus because msvc is known to not define it properly.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2019-12-01 17:45:19 UTC  
> Url: https://github.com/boostorg/core/pull/67#issuecomment-560136003  

> I don't think `-fsyntax-only` is relevant in practice.  
  
It is very relevant for header-only things.  
  
> because it relies on undocumented implementation details. This is similar to how I avoid relying on undefined behavior that works.  
  
You can rely on implementation things, and libstdc++ headers are already relying that `std::move` and `std::swap` are defined in those headers. Yes it could change in future, but it is not a big problem to fix that, and by that time it will save bunch of compile time.  
  
> I don't mind checking BOOST_MSVC in addition to __cplusplus because msvc is known to not define it properly.  
  
It is ok to rely on MSVC implementation details but not for others... I do not understand what are the differences.

---

## Comment 5

> Username: Kojoley  
> Created_at: 2019-12-01 17:46:29 UTC  
> Url: https://github.com/boostorg/core/pull/67#issuecomment-560136127  

`-std=c++03` values  
  
compiler            | cur  | c++11| this  
--------------------|------|------|-----  
gcc 8.3             | 0.06 | 0.05 | 0.01  
clang 9             | 0.08 | 0.08 | 0.02  
clang 9 libc++      | 0.35 | 0.35 | 0.12  
mingw (gcc 9.2)     | 0.06 | 0.06 | 0.01

---

## Comment 6

> Username: Lastique  
> Created_at: 2019-12-01 18:50:40 UTC  
> Url: https://github.com/boostorg/core/pull/67#issuecomment-560144199  

> You can rely on implementation things, and libstdc++ headers are already relying that `std::move` and `std::swap` are defined in those headers.  
  
Those headers (the ones in `bits`) are libstdc++ implementation details. Of course it includes its own internal headers.  
  
I'm all for reducing compile times, but I'm hesitant about including internal headers of the standard library. Not only because they may change without notice, but also because there may be specific conventions about including them (e.g. they may require some macros defined or other headers included before them).

---

## Comment 7

> Username: glenfe  
> Created_at: 2019-12-01 19:09:07 UTC  
> Url: https://github.com/boostorg/core/pull/67#issuecomment-560146232  

I'm not in favor of the use of <type_traits> or the use of any of the headers in libstdc++ <bits/etcetera.h>  for swap, just for decreasing compilation times.

---

## Comment 8

> Username: Kojoley  
> Created_at: 2019-12-01 21:45:29 UTC  
> Url: https://github.com/boostorg/core/pull/67#issuecomment-560163415  

Ok. Since this change gives `boost/optional.hpp` header a free 4% speed-up I will suggest Boost.Optional maintainer to use swap from Boost.Move.

---

## Comment 9

> Username: Lastique  
> Created_at: 2019-12-01 22:50:39 UTC  
> Url: https://github.com/boostorg/core/pull/67#issuecomment-560169864  

One option could be to implement default `std::swap` ourselves and not include anything at all.

---

## Review 10 [Commented]

> Username: pdimov  
> Created_at: 2019-12-01 23:46:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/67#pullrequestreview-324973327  

📁 include/boost/core/swap.hpp

```diff
  41 |- #else // C++98/03 fallback
  42 |- # include <algorithm>
  26 |+ #if __cplusplus >= 201103L
```

> Username: pdimov  
> Created_at: 2019-12-01 23:46:37 UTC  
> Url: https://github.com/boostorg/core/pull/67#discussion_r352383434  

Will you add `|| defined(BOOST_MSVC)` here or shall I?

> Username: Kojoley  
> Created_at: 2019-12-01 23:49:36 UTC  
> Updated_at: 2019-12-01 23:49:37 UTC  
> Url: https://github.com/boostorg/core/pull/67#discussion_r352383644  

I am fine if you take the action on this.


---

## Comment 11

> Username: pdimov  
> Created_at: 2019-12-02 04:00:19 UTC  
> Url: https://github.com/boostorg/core/pull/67#issuecomment-560219007  

> One option could be to implement default `std::swap` ourselves and not include anything at all.  
  
That might work. It won't find user specializations of `std::swap` though. I'm not sure whether those exist.

---

## Comment 12

> Username: glenfe  
> Created_at: 2019-12-02 05:11:57 UTC  
> Url: https://github.com/boostorg/core/pull/67#issuecomment-560233666  

> > One option could be to implement default `std::swap` ourselves and not include anything at all.  
>   
> That might work. It won't find user specializations of `std::swap` though. I'm not sure whether those exist.  
  
Not allowed to exist going forward since P0551R3 was adopted.

---

## Comment 13

> Username: pdimov  
> Created_at: 2019-12-02 05:40:51 UTC  
> Url: https://github.com/boostorg/core/pull/67#issuecomment-560240586  

>Marshall Clow 2018-03-19 04:13:14 UTC  
>Adopted in Jacksonville.  
  
On the other hand, https://github.com/boostorg/core/blob/develop/test/swap/specialized_in_std.cpp

---

## Comment 14

> Username: Lastique  
> Created_at: 2019-12-02 08:43:22 UTC  
> Url: https://github.com/boostorg/core/pull/67#issuecomment-560292886  

> It won't find user specializations of `std::swap` though.  
  
I think it will.  
  
```  
namespace boost_swap_impl {  
  
template< typename T >  
void swap(T const& cleft, T const& cright) // use const to make the overload less preferable  
{  
  // Cast away the const and perform the swap  
}  
  
template< typename T >  
void swap_impl(T& left, T& right)  
{  
  using namespace std; // brings in std::swap specializations  
  swap(left, right);  
}  
  
} // namespace boost_swap_impl  
```  
  
Even if it doesn't work, I'm inclined to mark `std::swap` specializations unsupported. It's not the right way to add support for swapping to user's types anyway.

---

## Comment 15

> Username: Lastique  
> Created_at: 2019-12-02 08:46:09 UTC  
> Url: https://github.com/boostorg/core/pull/67#issuecomment-560293921  

Also, we should mark `swap` and its implementation `noexcept` appropriately. But that's another matter.

---

## Comment 16

> Username: Kojoley  
> Created_at: 2019-12-02 10:40:40 UTC  
> Url: https://github.com/boostorg/core/pull/67#issuecomment-560338780  

> Also, we should mark `swap` and its implementation `noexcept` appropriately. But that's another matter.  
  
And make it `constexpr`.

---

## Comment 17

> Username: pdimov  
> Created_at: 2019-12-02 15:25:32 UTC  
> Url: https://github.com/boostorg/core/pull/67#issuecomment-560444091  

> I think it will.  
  
This will either find or not find `std::swap` depending on header order, I think, which may technically lead to ODR violations.  
  
Just using our own implementation and ignoring specializations would be easier for getting noexcept/constexpr correct, although our self-imposed inability to use TypeTraits won't help. Using f.ex. `noexcept(noexcept(T((T&&)lhs)) && noexcept(lhs = (T&&)rhs))` is not quite the same as using the traits because it SFINAEs which will affect overload resolution and may go to some unintended ADL swap.  
  
We could use `<type_traits>` I suppose but this kind of goes against our other goal, to not include anything. :-)

---

## Comment 18

> Username: pdimov  
> Created_at: 2019-12-02 17:43:34 UTC  
> Url: https://github.com/boostorg/core/pull/67#issuecomment-560502059  

On second look, `std::swap` is SFINAEd out too, http://eel.is/c++draft/utility.swap#1. So maybe writing the noexcept in the above manner will be correct after all.

---

## Comment 19

> Username: Lastique  
> Created_at: 2019-12-02 19:15:45 UTC  
> Updated_at: 2019-12-02 19:17:20 UTC  
> Url: https://github.com/boostorg/core/pull/67#issuecomment-560539667  

Even if it wasn't, I don't see a problem with disabling incorrect instantiation where the standard only requires a hard error. You'll get a hard error either way if you call it on a non-copyable non-movable type, SFINAE only provides an additional benefit of providing a way to detect if the type is swappable (i.e. if the `swap` call is valid).  
  
Also, `boost::swap` doesn't have to reproduce `std::swap` to the letter. It already does a different thing.

---

## Comment 20

> Username: pdimov  
> Created_at: 2019-12-02 19:25:15 UTC  
> Url: https://github.com/boostorg/core/pull/67#issuecomment-560543333  

Not quite because in theory at least dropping the primary overload may instead of giving an error call some user-defined overload using some unwanted implicit conversions. (`swap(Base&, Base&)` for `Derived`, for instance.)  
  
But that's academic, because the standard already requires it to be dropped, so we already inherit this behavior whether we like it or not. :-)

---

## Comment 21

> Username: pdimov  
> Created_at: 2019-12-05 00:01:45 UTC  
> Url: https://github.com/boostorg/core/pull/67#issuecomment-561901722  

For future reference, if we want to pursue defining our own swap and foregoing the inclusion of any header, this is where swap.hpp is being used:  
```  
C:\boost-git\develop\libs\core>..\..\dist\bin\boostdep boost/core/swap.hpp  
Inclusion report for <boost/core/swap.hpp> (in module core):  
  
    from integer:  
        <boost/integer/extended_euclidean.hpp>  
  
    from optional:  
        <boost/optional/detail/optional_swap.hpp>  
        <boost/optional/optional.hpp>  
  
    from thread:  
        <boost/thread/externally_locked.hpp>  
        <boost/thread/synchronized_value.hpp>  
  
  
C:\boost-git\develop\libs\core>..\..\dist\bin\boostdep boost/utility/swap.hpp  
Inclusion report for <boost/utility/swap.hpp> (in module core):  
  
    from flyweight:  
        <boost/flyweight/flyweight.hpp>  
        <boost/flyweight/refcounted.hpp>  
  
    from property_tree:  
        <boost/property_tree/detail/ptree_implementation.hpp>  
  
    from signals2:  
        <boost/signals2/detail/foreign_ptr.hpp>  
  
    from tuple:  
        <boost/tuple/detail/tuple_basic.hpp>  
```  
So these libraries would have to be checked after the change.

---
