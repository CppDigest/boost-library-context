# #355 - error: ‘is_same_v’ is not a member of ‘std’ [Closed]

> Username: rilesdg3  
> Created at: 2020-05-19 15:09:20 UTC  
> Updated at: 2020-05-20 00:50:16 UTC  
> Closed at: 2020-05-20 00:50:16 UTC  
> Url: https://github.com/boostorg/math/issues/355  

In univariate_statistics.hpp line 32 **else if constexpr (std::is_same_v<typename**   
  
is using **std::is_same_v** which is not a part of std:: in c++11, and c++14. The readme file says requires at least c++11, I think you might want std::is_same or std::experimental::is_same_v if the file is to work in c++11, and c++14?  
  
If you use std::experimental::is_same_v you will have to  
  
#include <experimental/type_traits>

---

## Comment 1

> Username: ckormanyos  
> Created at: 2020-05-19 15:31:27 UTC  
> Url: https://github.com/boostorg/math/issues/355#issuecomment-630899820  

> If you use std::experimental::is_same_v you will have to  
  
Correct.  
  
`is_same_v` came in C++17, as did several other helper templates in `<type_traits>`.  
  
I wonder if Boost has a compiler conditional that does the same?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-05-19 16:08:38 UTC  
> Url: https://github.com/boostorg/math/issues/355#issuecomment-630921514  

That file requires C++17 anyway - note the `else if constexpr`.  
  
@NAThompson is the requirement documented?  
  
We should probably add some PP logic with a #error when `BOOST_NO_CXX17_IF_CONSTEXPR` is defined, or when `__cpp_lib_type_trait_variable_templates ` is not defined?

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-05-19 17:04:22 UTC  
> Url: https://github.com/boostorg/math/issues/355#issuecomment-630953649  

It's definitely intentional, without `if constexpr` the functions don't work on integer types.  
  
I'll document this, and also change the namespacing to `namespace boost::math::statistics` so that we have a giant flag that this requires C++17.

---

## Comment 4

> Username: rilesdg3  
> Created at: 2020-05-19 17:47:26 UTC  
> Url: https://github.com/boostorg/math/issues/355#issuecomment-630978707  

I changed it to std::experemental::is_same_v and I am able to compile and run it with -std=c++14. I don't how it works in c++14 because of the if constexpr is c++17 but it does

---

## Comment 5

> Username: jzmaddock  
> Created at: 2020-05-19 17:51:49 UTC  
> Url: https://github.com/boostorg/math/issues/355#issuecomment-630980935  

>and also change the namespacing to namespace boost::math::statistics so that we have a giant flag that this requires C++17.  
  
@NAThompson that would be a breaking change:  is the namespace change really required?

---

## Comment 6

> Username: NAThompson  
> Created at: 2020-05-19 18:30:00 UTC  
> Updated at: 2020-05-19 18:42:24 UTC  
> Url: https://github.com/boostorg/math/issues/355#issuecomment-631001184  

@jzmaddock : It is not required; however, it is one of the simplest C++17 features, and so there aren't, to my knowledge, any C++17 compilers that don't support it.

---

## Comment 7

> Username: NAThompson  
> Created at: 2020-05-20 00:50:16 UTC  
> Url: https://github.com/boostorg/math/issues/355#issuecomment-631168516  

Fixed in develop.
