# #105 - log/example/async_log/main.cpp calls make_attr_ordering incorrectly [Closed]

> Username: StephanTLavavej  
> Created at: 2020-03-03 18:20:47 UTC  
> Updated at: 2020-03-03 20:18:46 UTC  
> Closed at: 2020-03-03 20:04:26 UTC  
> Url: https://github.com/boostorg/log/issues/105  

Microsoft's Visual C++ team regularly builds Boost as part of our "Real World Code" test suite, using the MSVC toolchain that's under development. This allows us to find and fix compiler/Standard Library regressions before they can affect you, and it also allows us to provide advance notice of source breaking changes.  
  
Recently, our test coverage with `/std:c++latest` has found code in Boost.Log that doesn't compile. This appears to be a Boost source issue. (The Boost build was blocked by a compiler bug that was fixed recently, which probably explains why we're seeing this now and not earlier.)  
  
Here's the error message (our test team prepared a preprocessed file):  
  
```  
cl /TP /std:c++latest /EHsc main.i  
main.i  
libs\log\example\async_log\main.cpp(89): error C2672: 'boost::log::v2_mt_nt6::make_attr_ordering': no matching overloaded function found  
libs\log\example\async_log\main.cpp(89): error C2783: 'boost::log::v2_mt_nt6::attribute_value_ordering<ValueT,FunT> boost::log::v2_mt_nt6::make_attr_ordering(const boost::log::v2_mt_nt6::attribute_name &,const FunT &)': could not deduce template argument for 'ValueT'  
.\boost/log/utility/record_ordering.hpp(186): note: see declaration of 'boost::log::v2_mt_nt6::make_attr_ordering'  
```  
  
Here's the call to `make_attr_ordering`:  
  
https://github.com/boostorg/log/blob/a7b4a140d97b725f03adfd123c7e097c0838a5fa/example/async_log/main.cpp#L86-L89  
  
Note that there are no explicit template arguments. Then, here's the declaration:  
  
https://github.com/boostorg/log/blob/a7b4a140d97b725f03adfd123c7e097c0838a5fa/include/boost/log/utility/record_ordering.hpp#L185-L186  
  
Here, `ValueT` is the first template parameter, but it doesn't appear in the function arguments, so it must be explicitly provided. The compiler is correctly complaining that `ValueT` hasn't been explicitly provided, and cannot be deduced.  
  
If I'm missing something and this is actually a compiler/Standard Library issue, please let me know so we can fix MSVC. Thanks!

---

## Comment 1

> Username: Lastique  
> Created at: 2020-03-03 19:40:04 UTC  
> Url: https://github.com/boostorg/log/issues/105#issuecomment-594131132  

There's another overload that doesn't require the explicit `ValueT` template parameter here:  
  
https://github.com/boostorg/log/blob/a7b4a140d97b725f03adfd123c7e097c0838a5fa/include/boost/log/utility/record_ordering.hpp#L214-L215  
  
That overload can be disabled by SFINAE, but in this case it should be enabled. I suspect, something in MSVC (either the compiler, or `std::less`) might be causing SFINAE failure and subsequent compilation error. Since this is your internal MSVC build, I cannot investigate.  
  
For the record, gcc 9 and clang 9 on Linux are able to compile this example.

---

## Comment 2

> Username: StephanTLavavej  
> Created at: 2020-03-03 19:56:12 UTC  
> Url: https://github.com/boostorg/log/issues/105#issuecomment-594138678  

Aha, thanks! It appears to be looking for `first_argument_type` etc. which explains what happened - we implemented C++20’s removal of the deprecated adaptor typedefs (with an escape hatch).  
  
This unblocks our testing; I’ll tell our test team to activate the escape hatch to restore the typedefs. For the long-term, I recommend refactoring Boost.Log to remove its dependency on the deprecated/removed typedefs.

---

## Comment 3

> Username: Lastique  
> Created at: 2020-03-03 20:04:25 UTC  
> Updated at: 2020-03-03 20:04:43 UTC  
> Url: https://github.com/boostorg/log/issues/105#issuecomment-594142195  

The other overload is for the case when deducing the function object argument type is not possible. Without the typedefs users will have to explicitly specify `ValueT`.

---

## Comment 4

> Username: Lastique  
> Created at: 2020-03-03 20:18:45 UTC  
> Url: https://github.com/boostorg/log/issues/105#issuecomment-594148289  

BTW, it might be helpful if the compiler also listed discarded overloads, with reasons why they were discarded, like gcc does in cases like these. This is very helpful in debugging SFINAE-related issues.
