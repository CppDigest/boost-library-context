# #619 - 'float_denorm_style' is deprecated [Closed]

> Username: NAThompson  
> Created at: 2024-05-05 18:05:40 UTC  
> Updated at: 2026-02-06 11:30:11 UTC  
> Closed at: 2026-02-06 11:30:11 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/619  

Compiling with clang in C++23 mode, we get the following warning:  
  
```  
/opt/homebrew/opt/boost@1.85/include/boost/multiprecision/cpp_bin_float.hpp:2272:11: warning: 'float_denorm_style' is deprecated [-Wdeprecated-declarations]  
 2272 | constexpr float_denorm_style numeric_limits<boost::multiprecision::number<boost::multiprecision::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>, ExpressionTemplates> >::has_denorm;  
      |           ^  
/opt/homebrew/opt/llvm/bin/../include/c++/v1/limits:129:6: note: 'float_denorm_style' has been explicitly marked deprecated here  
  129 | enum _LIBCPP_DEPRECATED_IN_CXX23 float_denorm_style {  
      |      ^  
```  
  
Low priority obviously. . .

---

## Comment 1

> Username: ckormanyos  
> Created at: 2025-06-28 19:31:47 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/619#issuecomment-3015996638  

I think we handled this a while back.  
  
Can we close?  
  
Cc: @jzmaddock and @mborland and @NAThompson

---

## Comment 2

> Username: ckormanyos  
> Created at: 2025-06-28 19:32:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/619#issuecomment-3015996905  

Wait a minute. We handled it for MSVC _ONLY_. What's up with clang? on this thing?

---

## Comment 3

> Username: jzmaddock  
> Created at: 2025-06-29 10:16:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/619#issuecomment-3016547221  

We can disable the warning, but I guess at some point std lib's will actually start removing this altogether?  
  
So I guess we could add a config macro BOOST_NO_FLOAT_DENORM_STYLE and stop adding this member altogether, but I worry that might break third party code too :(

---

## Comment 4

> Username: ckormanyos  
> Created at: 2026-02-06 11:30:11 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/619#issuecomment-3859915353  

> Wait a minute. We handled it for MSVC ONLY. What's up with clang? on this thing?  
  
Code-level warning disable also handled for clang now. See #751 and #752
