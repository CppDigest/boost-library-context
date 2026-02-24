# #105 - span breaks range (msvc, 2017) [Closed]

> Username: timblechmann  
> Created at: 2022-02-08 08:01:16 UTC  
> Updated at: 2022-05-14 07:21:02 UTC  
> Closed at: 2022-05-14 00:53:50 UTC  
> Url: https://github.com/boostorg/core/issues/105  

```  
#include <string>  
  
#include <boost/algorithm/string/case_conv.hpp>  
#include <boost/span.hpp>  
  
int foo()  
{  
    std::string str = "yada";  
    auto sp = boost::span{str}; // remove this line and the compile error goes away  
  
    boost::algorithm::to_lower(str);  
  
    return 1;  
}  
```  
  
with msvc (2019 and 2022) in c++17 mode (but not in c++20) gives:  
```  
C:/data/libraries/installed/x64-windows/include\boost/algorithm/string/detail/case_conv.hpp(105): error C3861: 'begin': identifier not found  
C:/data/libraries/installed/x64-windows/include\boost/algorithm/string/case_conv.hpp(98): note: see reference to function template instantiation 'void boost::algorithm::detail::transform_range<boost::iterator_range<std::_String_iterator<std::_String_val<std::_Simple_types<_Elem>>>>,boost::algorithm::detail::to_lowerF<char>>(const RangeT &,FunctorT)' being compiled  
        with  
        [  
            _Elem=char,  
            RangeT=boost::iterator_range<std::_String_iterator<std::_String_val<std::_Simple_types<char>>>>,  
            FunctorT=boost::algorithm::detail::to_lowerF<char>  
        ]  
<source>(406): note: see reference to function template instantiation 'void boost::algorithm::to_lower<std::string>(WritableRangeT &,const std::locale &)' being compiled  
        with  
        [  
            WritableRangeT=std::string  
        ]  
C:/data/libraries/installed/x64-windows/include\boost/algorithm/string/detail/case_conv.hpp(105): error C2672: 'boost::span<char,18446744073709551615>::begin': no matching overloaded function found  
C:/data/libraries/installed/x64-windows/include\boost/algorithm/string/detail/case_conv.hpp(106): error C3861: 'end': identifier not found  
C:/data/libraries/installed/x64-windows/include\boost/algorithm/string/detail/case_conv.hpp(106): error C2672: 'boost::span<char,18446744073709551615>::end': no matching overloaded function found  
C:/data/libraries/installed/x64-windows/include\boost/algorithm/string/detail/case_conv.hpp(107): error C3861: 'begin': identifier not found  
C:/data/libraries/installed/x64-windows/include\boost/algorithm/string/detail/case_conv.hpp(107): error C2672: 'boost::span<char,18446744073709551615>::begin': no matching overloaded function found  
C:/data/libraries/installed/x64-windows/include\boost/algorithm/string/detail/case_conv.hpp(104): error C2672: 'std::transform': no matching overloaded function found  
C:/data/libraries/installed/x64-windows/include\boost/algorithm/string/detail/case_conv.hpp(108): error C2780: '_FwdIt3 std::transform(_ExPo &&,const _FwdIt1,const _FwdIt1,const _FwdIt2,_FwdIt3,_Fn) noexcept': expects 6 arguments - 2 provided  
C:/data/msvc/14.31.31103-Pre/include\algorithm(3064): note: see declaration of 'std::transform'  
C:/data/libraries/installed/x64-windows/include\boost/algorithm/string/detail/case_conv.hpp(108): error C2780: '_OutIt std::transform(const _InIt1,const _InIt1,const _InIt2,_OutIt,_Fn)': expects 5 arguments - 2 provided  
C:/data/msvc/14.31.31103-Pre/include\algorithm(3045): note: see declaration of 'std::transform'  
C:/data/libraries/installed/x64-windows/include\boost/algorithm/string/detail/case_conv.hpp(108): error C2780: '_FwdIt2 std::transform(_ExPo &&,const _FwdIt1,const _FwdIt1,_FwdIt2,_Fn) noexcept': expects 5 arguments - 2 provided  
C:/data/msvc/14.31.31103-Pre/include\algorithm(3040): note: see declaration of 'std::transform'  
C:/data/libraries/installed/x64-windows/include\boost/algorithm/string/detail/case_conv.hpp(108): error C2780: '_OutIt std::transform(const _InIt,const _InIt,_OutIt,_Fn)': expects 4 arguments - 2 provided  
C:/data/msvc/14.31.31103-Pre/include\algorithm(3024): note: see declaration of 'std::transform'  
Compiler returned: 2  
```  
  
https://godbolt.org/z/zWjTKs8zK

---

## Comment 1

> Username: Lastique  
> Created at: 2022-02-08 12:38:22 UTC  
> Updated at: 2022-02-08 12:40:06 UTC  
> Url: https://github.com/boostorg/core/issues/105#issuecomment-1032564269  

I suspect, it is these that are causing the problem:  
  
https://github.com/boostorg/core/blob/bae7c049b0ae8bc0004d1c32966107432e13fa73/include/boost/core/span.hpp#L350-L356  
  
Are these needed anyway? Default `boost::begin`/`boost::end` from Boost.Range should work for anything that has member `begin`/`end`. Ditto std counterparts.  
  
PS: It does look like a compiler bug and should be reported to MS.

---

## Comment 2

> Username: thebrandre  
> Created at: 2022-05-13 17:40:08 UTC  
> Url: https://github.com/boostorg/core/issues/105#issuecomment-1126290355  

Here is a very minimalistic example [godbolt](https://godbolt.org/z/e5fTYs4zv).  
Note that `std::begin` works but `boost::begin` doesn't. This issue is present in VS2017, VS2019, VS2022, which unfortunately makes `boost::span` unusable with msvc or portable code.  
  
It would be nice to find a workaround. ;-)  
Alternatively, one might consider adding a compiler warning to `span.hpp` when compiled with msvc because this very hard to find and will potentially cost users lots of time (as it was the case with me).

---

## Comment 3

> Username: pdimov  
> Created at: 2022-05-13 17:52:06 UTC  
> Url: https://github.com/boostorg/core/issues/105#issuecomment-1126298569  

@glenfe ?

---

## Comment 4

> Username: glenfe  
> Created at: 2022-05-13 19:41:58 UTC  
> Url: https://github.com/boostorg/core/issues/105#issuecomment-1126393790  

I'll look at this tomorrow.

---

## Comment 5

> Username: thebrandre  
> Created at: 2022-05-13 19:53:43 UTC  
> Updated at: 2022-05-13 19:58:34 UTC  
> Url: https://github.com/boostorg/core/issues/105#issuecomment-1126408713  

This might be a helpful information: I just noticed that one can make it work in VS2019 and VS2022 if the compiler option [`/permissive-`](https://docs.microsoft.com/en-us/cpp/build/reference/permissive-standards-conformance?view=msvc-170) is set (demonstrated [here on godbolt](https://godbolt.org/z/MMzbzWjrv)). So it is (at least in part) an intended compiler bug.  
  
But I guess, it is not easy to test for `/permissive-`. There is a [feature request at Microsoft](https://developercommunity.visualstudio.com/t/pre-define-a-macro-when-compiling-under-permissive/1253982) and it's *Under Review* for more than a year by now.  
  
Thanks so much for looking into this!

---

## Comment 6

> Username: pdimov  
> Created at: 2022-05-13 21:08:13 UTC  
> Url: https://github.com/boostorg/core/issues/105#issuecomment-1126511153  

It's definitely a compiler bug, but as pointed out above by @Lastique, maybe we don't need these functions at all, which would sidestep this issue as a side effect.

---

## Comment 7

> Username: glenfe  
> Created at: 2022-05-13 21:40:01 UTC  
> Url: https://github.com/boostorg/core/issues/105#issuecomment-1126528873  

Affirmative on MSVC defect. Here's an example without `span` at all: https://godbolt.org/z/44PK5Gxnf  
  
We probably don't need these functions. I'm deciding whether to only omit them when BOOST_MSVC is defined, or unconditionally remove them.

---

## Comment 8

> Username: glenfe  
> Created at: 2022-05-13 22:17:33 UTC  
> Url: https://github.com/boostorg/core/issues/105#issuecomment-1126548431  

It turns out they were removed from the paper at some point so I'm going to unconditionally remove them.

---

## Comment 9

> Username: glenfe  
> Created at: 2022-05-14 00:53:50 UTC  
> Url: https://github.com/boostorg/core/issues/105#issuecomment-1126597068  

Resolved in ebff02a932cfb04d782edbd406a461ba47a915a7

---

## Comment 10

> Username: thebrandre  
> Created at: 2022-05-14 07:21:02 UTC  
> Url: https://github.com/boostorg/core/issues/105#issuecomment-1126661694  

Works just fine with msvc VS2017, VS2019, and VS2022 independent of `/permissive-`:  
https://godbolt.org/z/oP39G9dr8  
  
Thanks @glenfe!
