# #112 Partial Revert "std::tuple support (Resolving #103) (#104)" [Closed]

> Username: sgn  
> Created at: 2022-11-27 14:36:36 UTC  
> Updated at: 2025-05-25 13:25:26 UTC  
> Closed at: 2025-05-25 13:24:19 UTC  
> Url: https://github.com/boostorg/phoenix/pull/112  

This partial reverts commit 8b6a9c26c115bc2cefea300b5c0abf7edf6dd9b7.  
  
The being-reverted-change put uarg{1..10} in all translation unit that include "boost/phoenix/stl.hpp", should that file is being included by multiple translation unit, each of those translation units will have one definition of each of uarg{1..10}. Thus, we'll run into below error:  
  
> multiple definition of `boost::phoenix::placeholders::uarg1'  
  
Partial Revert the change in question by removing  
"boost/phoenix/stl/tuple.hpp" from "boost/phoenix/stl.hpp".  
  
Fix #111

---

## Comment 1

> Username: sgn  
> Created_at: 2022-11-27 14:37:36 UTC  
> Url: https://github.com/boostorg/phoenix/pull/112#issuecomment-1328260318  

@beojan

---

## Comment 2

> Username: sgn  
> Created_at: 2022-11-27 14:50:04 UTC  
> Url: https://github.com/boostorg/phoenix/pull/112#issuecomment-1328262586  

Since we're in bug-fix period for 1.81.0, I wouldn't want to revert a whole PR, which someone maybe rely on already

---

## Comment 3

> Username: beojan  
> Created_at: 2022-11-28 17:30:41 UTC  
> Updated_at: 2022-11-28 17:31:25 UTC  
> Url: https://github.com/boostorg/phoenix/pull/112#issuecomment-1329476575  

@sgn Does changing the `uargX` definition in `stl/tuple.hpp` to be `const` work?  
  
That's on line 113.

---

## Comment 4

> Username: sgn  
> Created_at: 2022-11-28 17:41:33 UTC  
> Updated_at: 2022-11-28 17:42:52 UTC  
> Url: https://github.com/boostorg/phoenix/pull/112#issuecomment-1329491117  

With `g++-12 -std=c++14` `const` yes, `constexpr` no.  
I'm not a language lawyer hence I'm not sure what standard would say about this.  
If you can quote something from standard that make `const` legal, I would happy to change the PR, I won't want to have a broken release for some compilers.  
  
```sh  
% cat test.cpp                                                                                         
#include <boost/phoenix/stl/tuple.hpp>  
  
int func();  
  
int main() {  
        return func();  
}  
@selene /tmp   
% cat other.cpp                                                                                        
#include <boost/phoenix/stl/tuple.hpp>  
  
int func();  
  
int func() {  
        return 0;  
}  
@selene /tmp   
% g++ -fPIC -std=c++14 -I /home/boost/include test.cpp other.cpp -o abc  
```  
  
```diff  
--- boost/phoenix/stl/tuple.hpp.orig    2022-11-29 00:42:17.884124579 +0700  
+++ boost/phoenix/stl/tuple.hpp 2022-11-29 00:40:35.163960550 +0700  
@@ -110,7 +110,7 @@  
     namespace placeholders {  
         #define BOOST_PP_LOCAL_LIMITS (1, BOOST_PHOENIX_ARG_LIMIT)  
         #define BOOST_PP_LOCAL_MACRO(N)                                                \  
-            auto uarg##N =                                                             \  
+            const auto uarg##N =                                                             \  
             boost::phoenix::get_<(N)-1>(boost::phoenix::placeholders::arg1);  
         #include BOOST_PP_LOCAL_ITERATE()  
     }  
```

---

## Comment 5

> Username: beojan  
> Created_at: 2022-11-28 17:51:02 UTC  
> Url: https://github.com/boostorg/phoenix/pull/112#issuecomment-1329509750  

`const` is what's used for the `argX` placeholders, which is why this problem didn't show up with those.  
  
I think the reason this works is that `const` variables have internal linkage so they can't be referred to from other translation units.

---

## Comment 6

> Username: djowel  
> Created_at: 2022-11-29 00:17:56 UTC  
> Url: https://github.com/boostorg/phoenix/pull/112#issuecomment-1329911913  

The checks are failing :-(

---

## Comment 7

> Username: sgn  
> Created_at: 2022-11-29 00:38:30 UTC  
> Url: https://github.com/boostorg/phoenix/pull/112#issuecomment-1329925469  

OK, I changed it into internal linkage.

---

## Comment 8

> Username: ytimenkov  
> Created_at: 2022-11-30 14:24:48 UTC  
> Url: https://github.com/boostorg/phoenix/pull/112#issuecomment-1332227866  

OOC: what is the problem with `constexpr`?

---

## Comment 9

> Username: sgn  
> Created_at: 2022-11-30 14:55:55 UTC  
> Url: https://github.com/boostorg/phoenix/pull/112#issuecomment-1332296136  

> OOC: what is the problem with `constexpr`?  
  
  
>./boost/phoenix/stl/tuple.hpp:114:40: error: call to non-'constexpr' function 'const typename boost::phoenix::expression::get_with_idx<boost::phoenix::tuple_detail::idx_wrap<N>, Tuple>::type boost::phoenix::get_(const Tuple&) [with int N = 1; Tuple = actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<argument<1> >, 0> >; typename expression::get_with_idx<tuple_detail::idx_wrap<N>, Tuple>::type = actor<boost::proto::exprns_::basic_expr<tag::get_with_idx, boost::proto::argsns_::list2<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<tuple_detail::idx_wrap<1> >, 0>, actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<argument<1> >, 0> > >, 2> >]'                                                            114 |             boost::phoenix::get_<(N)-1>(boost::phoenix::placeholders::arg1);

---

## Comment 10

> Username: ytimenkov  
> Created_at: 2022-11-30 18:42:14 UTC  
> Url: https://github.com/boostorg/phoenix/pull/112#issuecomment-1332584253  

Ah, I see... So it needs to be `constexpr` all the way...  And `inline` for variable is C++17 only...  
I worked this around by defining `BOOST_PHOENIX_STL_TUPLE_H_` :roll_eyes: . Trying to include only `bind.hpp` which is in use didn't compile either :(

---

## Comment 11

> Username: beojan  
> Created_at: 2022-11-30 18:44:12 UTC  
> Url: https://github.com/boostorg/phoenix/pull/112#issuecomment-1332586502  

> Ah, I see... So it needs to be `constexpr` all the way... And `inline` for variable is C++17 only... I worked this around by defining `BOOST_PHOENIX_STL_TUPLE_H_` roll_eyes . Trying to include only `bind.hpp` which is in use didn't compile either :(  
  
Did you have an issue with making the `uarg` definitions `const`, which @sgn reported works?

---

## Comment 12

> Username: djowel  
> Created_at: 2022-12-01 01:33:45 UTC  
> Url: https://github.com/boostorg/phoenix/pull/112#issuecomment-1333022388  

No `constexpr` please. Phoenix is supposed to work with older compilers.

---

## Comment 13

> Username: beojan  
> Created_at: 2022-12-01 01:45:45 UTC  
> Url: https://github.com/boostorg/phoenix/pull/112#issuecomment-1333032911  

As it stands this PR doesn't use `constexpr` (only `const`) so that should be fine.

---

## Comment 14

> Username: killerbot242  
> Created_at: 2022-12-16 17:49:14 UTC  
> Url: https://github.com/boostorg/phoenix/pull/112#issuecomment-1355304949  

has this been fixed ? Seems the release of 1.81 contains this problem,and breaks our code base ?

---

## Comment 15

> Username: djowel  
> Created_at: 2022-12-25 08:23:03 UTC  
> Url: https://github.com/boostorg/phoenix/pull/112#issuecomment-1364643993  

Can we please fix this? Perhaps we should just revert to the latest stable state.

---

## Comment 16

> Username: beojan  
> Created_at: 2022-12-25 12:27:34 UTC  
> Url: https://github.com/boostorg/phoenix/pull/112#issuecomment-1364674263  

Are the failed CI checks connected to the contents of this PR at all?

---

## Comment 17

> Username: sgn  
> Created_at: 2022-12-25 15:23:27 UTC  
> Url: https://github.com/boostorg/phoenix/pull/112#issuecomment-1364697910  

> Are the failed CI checks connected to the contents of this PR at all?  
  
Same error:  
```  
./boost/container_hash/is_described_class.hpp:10: fatal error: boost/describe/bases.hpp: No such file or directory  
```  
  
I think no, they ain't connected.

---

## Comment 18

> Username: fkonvick  
> Created_at: 2023-02-08 10:28:02 UTC  
> Url: https://github.com/boostorg/phoenix/pull/112#issuecomment-1422372236  

This happened to me with MSVC 2019 and Boost 1.81.0.  Including `<boost/phoenix/stl.hpp>` in multiple translation units resulted in multiple symbol definitions.  I was able to work around by replacing the original include in my _.cpp_ with  
```  
#include <boost/phoenix/stl/algorithm.hpp>  
#include <boost/phoenix/stl/container.hpp>  
```

---

## Comment 19

> Username: JohannesWilde  
> Created_at: 2025-05-25 09:41:19 UTC  
> Updated_at: 2025-05-25 09:41:36 UTC  
> Url: https://github.com/boostorg/phoenix/pull/112#issuecomment-2907721996  

This change was incorported with 665047aac26ad4d96b266d87504b3a88ad21b37e - I would like to propose to close this PR.

---

## Comment 20

> Username: saki7  
> Created_at: 2025-05-25 13:25:02 UTC  
> Url: https://github.com/boostorg/phoenix/pull/112#issuecomment-2907825336  

Thanks for the heads-up.

---
