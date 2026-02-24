# #354 - integral_constant is not convertible to bool [Closed]

> Username: ricejasonf  
> Created at: 2017-06-15 17:39:48 UTC  
> Updated at: 2018-02-27 12:56:26 UTC  
> Closed at: 2018-02-27 12:56:26 UTC  
> Url: https://github.com/boostorg/hana/issues/354  

I'm getting a compile error with the following call to `hana::is_convertible`:  
  
```cpp  
#include <boost/hana.hpp>  
  
namespace hana = boost::hana;  
  
static_assert(  
  hana::is_convertible<  
    hana::bool_<true>  
  , bool  
  >::value  
);  
```  
output:  
```  
/usr/local/include/boost/hana/core/to.hpp:36:22: error: ambiguous partial specializations of  
      'to_impl<bool, boost::hana::integral_constant<bool, true>, boost::hana::when<true> >'  
    struct to_impl : to_impl<To, From, when<true>> { };  
                     ^  
/usr/local/include/boost/hana/core/to.hpp:153:52: note: in instantiation of template class  
      'boost::hana::to_impl<bool, boost::hana::integral_constant<bool, true>, void>' requested here  
        static_cast<convert_detail::no_conversion>(*(to_impl<To, From>*)0)  
                                                   ^  
bool_conversion.cpp:6:9: note: during template argument deduction for class template partial  
      specialization 'is_convertible<type-parameter-0-0, type-parameter-0-1,  
      decltype((void)static_cast<convert_detail::no_conversion>(*(to_impl<To, From> *)0))>' [with From =  
      boost::hana::integral_constant<bool, true>, To = bool]  
  hana::is_convertible<  
        ^  
/usr/local/include/boost/hana/core/to.hpp:47:12: note: partial specialization matches [with To = bool,  
      From = boost::hana::integral_constant<bool, true>, condition = true]  
    struct to_impl<To, From, when<condition>> : convert_detail::no_conversion {  
           ^  
/usr/local/include/boost/hana/core/to.hpp:56:12: note: partial specialization matches [with To = bool,  
      From = boost::hana::integral_constant<bool, true>]  
    struct to_impl<To, From, when<convert_detail::is_valid<  
           ^  
/usr/local/include/boost/hana/core/to.hpp:171:12: note: partial specialization matches [with To = bool,  
      From = boost::hana::integral_constant<bool, true>]  
    struct to_impl<To, From, when<  
```  
  
Is this expected behaviour? I would at least expect it to compile and result in true/false.

---

## Comment 1

> Username: ldionne  
> Created at: 2017-06-22 16:48:32 UTC  
> Url: https://github.com/boostorg/hana/issues/354#issuecomment-310437856  

This is not expected. Taking a look.

---

## Comment 2

> Username: ricejasonf  
> Created at: 2017-06-23 17:24:17 UTC  
> Url: https://github.com/boostorg/hana/issues/354#issuecomment-310725087  

I can take a look too if you don't have time. Just let me know the expected result.  
  
(I would guess it should be convertible)

---

## Comment 3

> Username: ldionne  
> Created at: 2017-06-23 17:26:53 UTC  
> Url: https://github.com/boostorg/hana/issues/354#issuecomment-310725675  

So, I got the following test case:  
  
```c++  
// Copyright Louis Dionne 2013-2017  
// Distributed under the Boost Software License, Version 1.0.  
// (See accompanying file LICENSE.md or copy at http://boost.org/LICENSE_1_0.txt)  
  
#include <boost/hana/bool.hpp>  
#include <boost/hana/core/to.hpp>  
#include <boost/hana/integral_constant.hpp>  
namespace hana = boost::hana;  
  
  
static_assert(hana::is_convertible<hana::bool_<true>, bool>::value, "");  
static_assert(hana::is_convertible<hana::integral_constant<int, 1>, int>::value, "");  
static_assert(hana::is_convertible<hana::integral_constant<int, 1>, long>::value, "");  
  
int main() { }  
```  
  
The problem is that we have specializations of `hana::is_convertible` for stuff that can be `static_cast`ed to the underlying `value_type` (which is the case for `hana::integral_constant`), but also for models of the `hana::IntegralConstant` concept. They both match and we get the ambiguous specialization. We could simply add another specialization to disambiguate, or find a prettier solution. I wouldn't mind you thinking about that solution, otherwise I'll do it (but I have no ETA).

---

## Comment 4

> Username: ldionne  
> Created at: 2017-06-24 16:57:46 UTC  
> Url: https://github.com/boostorg/hana/issues/354#issuecomment-310850603  

> I can take a look too if you don't have time. Just let me know the expected result.  
>   
> (I would guess it should be convertible)  
  
I think it should be convertible, but not an embedding (since there's a loss of constexpr-ness).

---

## Comment 5

> Username: ricejasonf  
> Created at: 2017-06-24 19:37:13 UTC  
> Url: https://github.com/boostorg/hana/issues/354#issuecomment-310861349  

Then I'm tempted to say that this is incorrect (not the error itself):  
```  
/usr/local/src/hana/test/integral_constant/udl.cpp:28:1: error: static_assert failed ""  
static_assert(-1234_c == -1234ll, "");  
^             ~~~~~~~~~~~~~~~~~~  
1 error generated.  
```  
  
The specialization of `to_impl` for `Constant` makes it embedded with its `value_type`. Is that correct?

---

## Comment 6

> Username: ldionne  
> Created at: 2017-06-24 20:05:19 UTC  
> Url: https://github.com/boostorg/hana/issues/354#issuecomment-310862717  

Ahh, actually, the documentation for `Constant` says:  
  
> Any `Constant` `c` holding an underlying value of tag `T` is convertible to any tag `U` such that `T` is convertible to `U`. Specifically, the conversion is equivalent to  
>   
> ```c++  
> to<U>(c) == to<U>(value<decltype(c)>())  
> ```  
>   
> Also, those conversions are marked as an embedding whenever the conversion of underlying types is an embedding. This is to allow `Constant`s to inter-operate with constexpr objects easily:  
>   
> ```c++  
> plus(int_c<1>, 1) == 2  
> ```  
>   
> Strictly speaking, this is sometimes a violation of what it means to be an embedding. Indeed, while there exists an embedding from any `Constant` to a constexpr object (since `Constant` is just the canonical inclusion), there is no embedding from a `Constant` to a runtime object since we would lose the ability to define the value method (the constexprness of the object would have been lost). [...]  
  
Hence, the conversion should be marked as an embedding.

---

## Comment 7

> Username: ricejasonf  
> Created at: 2017-06-24 20:23:06 UTC  
> Url: https://github.com/boostorg/hana/issues/354#issuecomment-310863596  

I think this identifies a problem in that there can be discrepancies between `to` and `is_convertible` because `is_convertible` uses the raw type of `From` when calling `to_impl` directly. It should probably use the tag of `From`.

---

## Comment 8

> Username: ldionne  
> Created at: 2017-06-24 20:27:58 UTC  
> Url: https://github.com/boostorg/hana/issues/354#issuecomment-310863827  

The documentation for `is_convertible` says:  
  
> Specifically, `is_convertible<From, To>` is whether calling `to<To>` with an object of data type `From` would _not_ trigger a static assertion.  
  
IOW, `From` is already expected to be a tag. Is my reading incorrect?

---

## Comment 9

> Username: ricejasonf  
> Created at: 2017-06-24 20:31:00 UTC  
> Url: https://github.com/boostorg/hana/issues/354#issuecomment-310864005  

Ah, right. I think you explained that to me before as well. :sweat_smile:
