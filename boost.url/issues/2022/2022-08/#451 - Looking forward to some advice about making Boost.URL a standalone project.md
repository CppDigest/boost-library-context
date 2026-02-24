# #451 - Looking forward to some advice about making Boost.URL a standalone project. [Closed]

> Username: balusch  
> Created at: 2022-08-24 12:00:40 UTC  
> Updated at: 2022-09-27 20:45:33 UTC  
> Closed at: 2022-09-27 20:45:33 UTC  
> Url: https://github.com/boostorg/url/issues/451  

Hi, guys!  
  
Firstly, thanks for creating such a awesome project(again)!   
  
As said in https://github.com/CPPAlliance/url/issues/167, we are looking for a good URL library written in C++ and it seems that Boost.URL is the perfect choice.  
  
However, after some investigation in our project, I found that unfortunately we cannot use it directly since the Boost library we used currently cannot be upgraded to version 1.78 required by Boost.URL for some reason. And thus we decided to make Boost.URL standalone and synchronize with upstream periodically.  
  
Before taking actions, I hope you guys could give me some advice about makeing Boost.URL a standalone project in C++17(using GCC 11.2.1).   
  
Thanks in advance!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-24 14:02:25 UTC  
> Url: https://github.com/boostorg/url/issues/451#issuecomment-1225770519  

Well, try compiling Boost.URL with 1.78 and when you get an error paste it here and I will see what can be done to fix it.

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-08-24 16:40:18 UTC  
> Url: https://github.com/boostorg/url/issues/451#issuecomment-1225972139  

A standalone version is probably not that difficult if you have access to C++17. Here are the replacements you will probably need:  
  
> #include <boost/optional.hpp>  
  
```cpp  
#include <optional>  
```  
  
> #include <boost/core/detail/string_view.hpp>  
  
```cpp  
#include <string_view>  
```  
  
> #include <boost/variant2/variant.hpp>  
  
```cpp  
#include <variant>  
```  
  
`boost/variant2` is a little different from `std::variant` because it's never valueless. But I don't think it makes any difference in your case.  
  
> #include <boost/assert.hpp>  
  
You can replace that with `<cassert>` and a macro to replace `BOOST_ASSERT`  
  
> #include <boost/assert/source_location.hpp>  
  
```cpp  
#include <source_location>  
```   
  
 in C++20 or write your own in C++17. It's quite small.  
  
> #include <boost/config.hpp>  
> #include <boost/config/workaround.hpp>  
  
You won't need most things here in a compiler that supports C++17  
  
> #include <boost/throw_exception.hpp>  
  
You can decide what to do with exceptions instead of this header. In general, you can write a function that just throws the exceptions in standalone mode.  
  
> #include <boost/core/ignore_unused.hpp>  
  
This is a _very_ small function you can write. 10 lines of code at most.  
  
> #include <boost/type_traits/type_with_alignment.hpp>  
> #include <boost/type_traits/is_final.hpp>  
> #include <boost/type_traits/make_void.hpp>  
> #include <boost/mp11/algorithm.hpp>  
> #include <boost/mp11/function.hpp>  
> #include <boost/mp11/integer_sequence.hpp>  
> #include <boost/type_traits/remove_cv.hpp>  
> #include <boost/type_traits/copy_cv.hpp>  
> #include <boost/mp11/integral.hpp>  
> #include <boost/mp11/list.hpp>  
> #include <boost/mp11/tuple.hpp>  
  
In C++17, most of these things are available in `<type_traits>`. The others should be small to copy/paste in implementation details.  
  
> #include <boost/align/align_up.hpp>  
  
You might be able to replace this function with something in terms of `std::align`, but this function is so simple I think it's best to just copy/paste it.  
  
> #include <boost/system/error_code.hpp>  
> #include <boost/system/system_error.hpp>  
> #include <boost/system/result.hpp>  
  
This is probably the biggest problem. There's  
  
```cpp  
#include <error_code>  
```  
  
but `std::error_code` cannot store the source location as `boost::error_code` does, so you might need to adjust the macros that return errors.  
  
There's also no replacement for `result<T>` in C++17. The closest alternative to `std::result<T>` is `std::variant<T, std::error_code>` but the API is quite different. You might be able to write a small wrapper using `std::variant<T, std::error_code>` internally.

---

## Comment 3

> Username: balusch  
> Created at: 2022-08-25 00:33:32 UTC  
> Url: https://github.com/boostorg/url/issues/451#issuecomment-1226633948  

> Well, try compiling Boost.URL with 1.78 and when you get an error paste it here and I will see what can be done to fix it.  
  
@vinniefalco Hi, Vinnie. I don't quite understand what that means, maybe what I said before is not clear enough. I have no problem building Boost.URL with the most recent version of Boost, but want to get rid of Boost and make Boost.URL a completely standalone project in pure C++17(only STL) 😃.

---

## Comment 4

> Username: balusch  
> Created at: 2022-08-25 00:56:30 UTC  
> Updated at: 2022-08-25 00:59:50 UTC  
> Url: https://github.com/boostorg/url/issues/451#issuecomment-1226650375  

@alandefreitas Wow, that's what I need indeed, thanks!   
  
I'm not familiar with Boost(just have used several small components of it) and not very clear how the Boost components used in Boost.URL correspond to their substitutes in C++17 STL(especially the stuff under the `mp11` namespace and `system`), thanks for your detailed explanations, it really helps and now I think I could manage it 💪!  
  
I'll have a try following your instructions, could you please keep the issue open in case that I met any problem in the standalonization process?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-08-25 14:11:12 UTC  
> Url: https://github.com/boostorg/url/issues/451#issuecomment-1227317153  

My fault, I thought you said you had Boost 1.78. The docs for this lib are actually wrong, you need the latest Boost which is at the tip of develop. Alan's advice is good.
