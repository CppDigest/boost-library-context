# #248 - std::numeric_limits<>::max_digits10 is different between float128 and cpp_bin_float_quad [Closed]

> Username: pabristow  
> Created at: 2020-06-10 16:24:07 UTC  
> Updated at: 2020-06-17 17:51:42 UTC  
> Closed at: 2020-06-17 17:51:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/248  

Testing some code I had written, I stumbled over a difference between   
  
std::numeric_limits<>::max_digits10  
  
for float128 and cpp_bin_float_quad  
  
(when the string output from these types did not match in an exact comparison), for example:  
  
BOOST_TEST((std::numeric_limits<cpp_bin_float_quad>::max)() == (std::numeric_limits<float128>::max)());  
  
Drawing on   
https://en.cppreference.com/w/cpp/types/numeric_limits/max_digits10  
  
I wrote this tiny test  
  
std::cout << "\nstd::numeric_limits<float128>::max_digits10 = "  
      <<   std::numeric_limits<float128>::max_digits10   
     <<  "\nstd::numeric_limits<cpp_bin_float_quad>::max_digits10 = "   
      << std::numeric_limits<cpp_bin_float_quad>::max_digits10 << std::endl;  
  
    std::cout << "\nstd::numeric_limits<float128>::digits10 = "  
     << std::numeric_limits<float128>::digits10  
    <<  "\nstd::numeric_limits<cpp_bin_float_quad>::digits10 = "   
      << std::numeric_limits<cpp_bin_float_quad>::digits10   
     << std::endl;  
    std::cout << "\nstd::numeric_limits<float128>::digits = "  
      << std::numeric_limits<float128>::digits   
    <<  "\nstd::numeric_limits<cpp_bin_float_quad>::digits = "  
      <<  std::numeric_limits<cpp_bin_float_quad>::digits   
    << std::endl;  
    std::cout << "\nstd::ceil(std::numeric_limits<float128>::digits * std::log10(2) +1) = "  
      << std::ceil(std::numeric_limits<float128>::digits * std::log10(2) +1)  
      << "\nstd::ceil(std::numeric_limits<cpp_bin_float_quad>::digits * std::log10(2) +1) = "  
      << std::ceil(std::numeric_limits<cpp_bin_float_quad>::digits * std::log10(2) +1)  
  <<  "\nstd::ceil(std::numeric_limits<double>::digits * std::log10(2) +1) = "  
  << std::ceil(std::numeric_limits<double>::digits * std::log10(2) +1)  
  << std::endl;  
  
with output:  
  
  std::numeric_limits<float128>::max_digits10 = 36  
  std::numeric_limits<cpp_bin_float_quad>::max_digits10 = 37  
  
  std::numeric_limits<float128>::digits10 = 33  
  std::numeric_limits<cpp_bin_float_quad>::digits10 = 33  
  
  std::numeric_limits<float128>::digits = 113  
  std::numeric_limits<cpp_bin_float_quad>::digits = 113  
  
  std::ceil(std::numeric_limits<float128>::digits * std::log10(2) +1) = 36   
  std::ceil(std::numeric_limits<cpp_bin_float_quad>::digits * std::log10(2) +1) = 36  
  std::ceil(std::numeric_limits<double>::digits * std::log10(2) +1) = 17  
  
  
the max_digits10 value is computed in  
  \boost\libs\multiprecision\include\boost\multiprecision\cpp_bin_float.hpp  
  
   BOOST_STATIC_CONSTEXPR int  max_digits10 = (digits * 301 / 1000) + 3;  
  
but I suspect that it would be better computed thus  
  
   std::ceil(std::numeric_limits<cpp_bin_float_*>::digits * std::log10(2) +1)  
  
The difference is unlikely to have any practical effect, especially as it is erring on the safe side.  
  
However a correction will trigger some test failures and perhaps cause user programs/test to fail,  
so it is not clear what if anything, we should do, except perhaps document the 'wrong' value?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-06-10 17:26:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/248#issuecomment-642151085  

In the header we have:  
  
```  
   // Is this really correct???  
   BOOST_STATIC_CONSTEXPR int  max_digits10 = (digits * 301 / 1000) + 3;  
```  
  
Is there a better definition?

---

## Comment 2

> Username: pabristow  
> Created at: 2020-06-11 08:33:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/248#issuecomment-642498933  

std::ceil(std::numeric_limits<cpp_bin_float_*>::digits * std::log10(2) +1)  
  
is recommended at  
  
https://en.cppreference.com/w/cpp/types/numeric_limits/max_digits10  
  
std::ceil(std::numeric_limits<float128>::digits * std::log10(2) +1) = 36  
std::ceil(std::numeric_limits<cpp_bin_float_quad>::digits * std::log10(2) +1) = 36  
  
I suspect that this expression was not an option with c++03?    
  
(Currently fails if I select std gnu98 with message  
#if __cplusplus < 201103L  
#error This file requires compiler and library support \  
for the ISO C++ 2011 standard. This support must be enabled \  
with the -std=c++11 or -std=gnu++11 compiler options.  
#endif  
  
but OK with gnu++11  (need gnu for float128), and up of course.  
  
So we can shift to using this formula when we require C++11.  
  
But the bigger question may be "Is it worth the potential annoyances?" Views?

---

## Comment 3

> Username: ckormanyos  
> Created at: 2020-06-11 09:18:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/248#issuecomment-642524578  

The compiler requires C++11 because those functions were injected into std in 11.  
  
Using those functions might be fine for built ins. But as these must be compile time constant, will be tough for multi precision tupes

---

## Comment 4

> Username: pabristow  
> Created at: 2020-06-11 11:52:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/248#issuecomment-642593842  

As you observe.  Annoyingly, GCC seems fine with constexpr std::ceil   
  
(as noted in http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2020/p0533r6.pdf)  
  
for example:  
   BOOST_STATIC_CONSTEXPR int mdq = std::ceil(std::numeric_limits<cpp_bin_float_quad>::digits * std::log10<>(2) + 1);  
  
whereas MSVC  VS2019 Preview 16.7.0 Preview 2 even with C++std = \latest complains that   
  
error C2131: expression did not evaluate to a constant  
  
Nothing portable using ceil can be constexpr, so this is a non-starter as you observe.  
  
However, I think from using this expression non-constexpr to calculate a few max_digits10, the 'fix' is simple:  
  
the added +3 should only be +2  
  
   BOOST_STATIC_CONSTEXPR int  max_digits10 = (digits * 301 / 1000) + 3;  
  
   BOOST_STATIC_CONSTEXPR int  max_digits10 = (digits * 301 / 1000) + 2;  
  
 gives the 'right' max_digits10 for cpp_bin_float_quad, _50 and _100  
  
quad max_digits10 = 36  (not 37)  
cpp_bin_float_50   = 52 (not 53)  
cpp_bin_float_100   = 102 (not 103)  
  
(where 'right' means the results of the Kahan equation)

---

## Comment 5

> Username: ckormanyos  
> Created at: 2020-06-11 14:44:11 UTC  
> Updated at: 2020-06-11 14:45:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/248#issuecomment-642704843  

> the added +3 should only be +2  
  
That is a good fix if it works. And it seems clearly compile-time constant.  
  
One more question... We discussed something like this a few years ago.  
  
I get it that you are finding a _best fit_ for a variety of types. I do, somehow, recall an exception for `float32_t`, where the added 3 was required. Does this need special handling for low digit counts? Or am I mixing something up from the past?  
  
Chris

---

## Comment 6

> Username: pabristow  
> Created at: 2020-06-11 15:34:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/248#issuecomment-642740817  

Yes - think that you are correct, but we don't need to worry about the fundamental float, double, long double types max_digits10 because the std library provides them.  It is only multiprecision max_digits10 that is an issue.  
  
I firmly believe that float128 and cpp_bin_float_quad should provide the same max_digits10, and thus the same operator >>output strings.  
  
Shall I change this on a local branch from multiprecision develop and run the tests and examples to see what breaks?  This might give clues to any collateral damage this might cause.

---

## Comment 7

> Username: jzmaddock  
> Created at: 2020-06-11 16:34:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/248#issuecomment-642794480  

As Chris says, I think there were cases that broke with + 2, I can't remember what they were, but it was a deliberate change it seems: https://github.com/boostorg/multiprecision/commit/7e457161e63e5b19b818e54e9e24da32d0bd2aff  
  
BTW I think the formula you gave involving ceil can be implemented without actually calling that function.... let me see....

---

## Comment 8

> Username: jzmaddock  
> Created at: 2020-06-11 16:41:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/248#issuecomment-642799387  

Is there a way to test a given formula for N digit counts without herculean round trip testing?

---

## Comment 9

> Username: jzmaddock  
> Created at: 2020-06-11 17:31:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/248#issuecomment-642827069  

See https://github.com/boostorg/multiprecision/pull/249

---

## Comment 10

> Username: pabristow  
> Created at: 2020-06-12 07:57:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/248#issuecomment-643131468  

I think brute force can be used easily to compare the two equations   
  
`const std::ceil(std::numeric_limits<cpp_bin_float_quad>::digits * std::log10<>(2) + 1);`  
  
with   
  
 `BOOST_STATIC_CONSTEXPR int  max_digits10 = (digits * 301 / 1000) + 2;`  
  
I just checked the commonly used cpp_bin_float_ quad, 50 and 100.  
  
Shall I do this, up to 1000 say?

---

## Comment 11

> Username: jzmaddock  
> Created at: 2020-06-12 17:24:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/248#issuecomment-643394907  

Ah, it fails for quite a few values - always one short :(  
  
I have a correction which is correct for every bit count up to about 15K, but then shows up quite a few where it falls short again.  
  
I'll experiment some more.
