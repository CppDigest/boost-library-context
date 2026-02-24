# #434 - More GCC warnings [Closed]

> Username: jzmaddock  
> Created at: 2022-02-06 18:41:10 UTC  
> Updated at: 2022-02-09 15:35:23 UTC  
> Closed at: 2022-02-09 15:35:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/434  

When compiling test_arithmetic_cpp_bin_float_2m.cpp with:  
  
```  
g++ -O3 -DNDEBUG -c test_arithmetic_cpp_bin_float_2m.cpp  
```  
  
 I see:  
  
```  
In file included from ../../../boost/multiprecision/cpp_int.hpp:2338,  
                 from ../../../boost/multiprecision/cpp_bin_float.hpp:11,  
                 from test_arithmetic_cpp_bin_float_2m.cpp:8:  
In function 'constexpr void boost::multiprecision::backends::left_shift_generic(Int&, boost::multiprecision::double_limb_type) [with Int = boost::multiprecision::backends::cpp_int_backend<0, 33  
24, boost::multiprecision::signed_magnitude, boost::multiprecision::unchecked, std::allocator<char> >]',  
    inlined from 'constexpr typename std::enable_if<(! boost::multiprecision::backends::is_trivial_cpp_int<boost::multiprecision::backends::cpp_int_backend<MinBits, MaxBits, SignType, Checked, Allocator> >::value)>::type boost::multiprecision::backends::eval_left_shift(boost::multiprecision::backends::cpp_int_backend<MinBits, MaxBits, SignType, Checked, Allocator>&, boost::multiprecision::double_limb_type) [with long long unsigned int MinBits1 = 0; long long unsigned int MaxBits1 = 3324; boost::multiprecision::cpp_integer_type SignType1 = boost::multiprecision::signed_magnitude; boost::multiprecision::cpp_int_check_type Checked1 = boost::multiprecision::unchecked; Allocator1 = std::allocator<char>]' at ../../../boost/multiprecision/cpp_int/bitwise.hpp:493:25,  
    inlined from 'constexpr typename std::enable_if<(! boost::multiprecision::backends::is_trivial_cpp_int<boost::multiprecision::backends::cpp_int_backend<MinBits, MaxBits, SignType, Checked, Allocator> >::value)>::type boost::multiprecision::backends::eval_left_shift(boost::multiprecision::backends::cpp_int_backend<MinBits, MaxBits, SignType, Checked, Allocator>&, boost::multiprecision::double_limb_type) [with long long unsigned int MinBits1 = 0; long long unsigned int MaxBits1 = 3324; boost::multiprecision::cpp_integer_type SignType1 = boost::multiprecision::signed_magnitude; boost::multiprecision::cpp_int_check_type Checked1 = boost::multiprecision::unchecked; Allocator1 = std::allocator<char>]' at ../../../boost/multiprecision/cpp_int/bitwise.hpp:445:1,  
    inlined from 'static std::numeric_limits<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>, ExpressionTemplates> >::number_type std::numeric_limits<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>, ExpressionTemplates> >::max() [with unsigned int Digits = 1000; boost::multiprecision::backends::digit_base_type DigitBase = boost::multiprecision::backends::digit_base_10; Allocator = std::allocator<char>; Exponent = long long int; Exponent MinE = 0; Exponent MaxE = 0; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_on]' at ../../../boost/multiprecision/cpp_bin_float.hpp:2090:28:  
../../../boost/multiprecision/cpp_int/bitwise.hpp:440:22: warning: 'void* __builtin_memset(void*, int, long long unsigned int)' writing 408 bytes into a region of size 32 overflows the destination [-Wstringop-overflow=]  
  440 |       pr[rs - 1 - i] = 0;  
      |       ~~~~~~~~~~~~~~~^~~  
In file included from test_arithmetic_cpp_bin_float_2m.cpp:8:  
../../../boost/multiprecision/cpp_bin_float.hpp: In static member function 'static std::numeric_limits<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>, ExpressionTemplates> >::number_type std::numeric_limits<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>, ExpressionTemplates> >::max() [with unsigned int Digits = 1000; boost::multiprecision::backends::digit_base_type DigitBase = boost::multiprecision::backends::digit_base_10; Allocator = std::allocator<char>; Exponent = long long int; Exponent MinE = 0; Exponent MaxE = 0; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_on]':  
../../../boost/multiprecision/cpp_bin_float.hpp:2088:97: note: destination object 'i' of size 32  
 2088 |             int_backend_type                                                                    i;  
      |                                                                                                 ^  
```  
  
The code is safe so far as I can see since `rs - 1 - i < result.size()` is necessarily true.  I've tried disabling with #pragmas but haven't found an invocation which works.  Anyone any ideas?

---

## Comment 1

> Username: ckormanyos  
> Created at: 2022-02-06 18:57:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/434#issuecomment-1030892393  

It's the kind of thing I have encountered before. This had also led me to some minutes ov review at times.  
  
I can reproduce the error on my deskttop with g++-10. I'll report if I find anything...

---

## Comment 2

> Username: ckormanyos  
> Created at: 2022-02-06 19:00:20 UTC  
> Updated at: 2022-02-06 19:00:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/434#issuecomment-1030892862  

I believe you can silence it with a full-file envelop around `cpp_int/bitwise.hpp` with something like:  
  
```cpp  
#if defined(__GNUC__) && !defined(__clang__)  
#pragma GCC diagnostic push  
#pragma GCC diagnostic ignored "-Wstringop-overflow"  
#endif  
  
// The content of the file cpp_int/bitwise.hpp  
  
#if defined(__GNUC__) && !defined(__clang__)  
#pragma GCC diagnostic pop  
#endif  
```  
  
We do, however, need to see if its at `(__GNUC >= 10)`, i.e, GCC 10 or higher, as I think it may be.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2022-02-06 19:06:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/434#issuecomment-1030893882  

> need to see if its at `(__GNUC >= 10)`, i.e, GCC 10 or higher, ...  
  
I took my local disable-fix from GCC 10 to 9 to 8 locally and it was OK on those compilers.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2022-02-06 19:23:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/434#issuecomment-1030897027  

Hmmm, that's the first thing I tried, and I still see the warning with gcc-11.

---

## Comment 5

> Username: ckormanyos  
> Created at: 2022-02-06 19:37:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/434#issuecomment-1030899357  

> Hmmm, that's the first thing I tried, and I still see the warning with gcc-11  
  
It is perplexing me as well. I went down to GCC 8 and found an unrelated, yet similar warning in another part of the code. I can not seem to suppress this warning either.  
  
The instance that I can not suppress does not explicitly mention a line number within our own headers, it simply mentions the note about the built-in memset function that seems to evoke the warning.  
  
Do you see any influence of the `#pragma`-disable sequence on any other compiler(s)?

---

## Comment 6

> Username: jzmaddock  
> Created at: 2022-02-07 15:13:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/434#issuecomment-1031576033  

Reduced test case is:  
  
```  
#include <boost/multiprecision/cpp_bin_float.hpp>   
#include <iostream>  
  
  
int main()  
{  
   std::cout << (std::numeric_limits<boost::multiprecision::number<boost::multiprecision::cpp_bin_float<1000, boost::multiprecision::digit_base_10, std::allocator<char> > > >::max)() << std::endl;  
   return 0;  
}  
```  
  
Note the dynamic allocator.  The 408 bytes is correct, we zeroing out 51 8 byte limbs, but the sizeof(i) is irrelevant, since we're going into dynamic memory, not the internal cache of the cpp_int_backend.

---

## Comment 7

> Username: jzmaddock  
> Created at: 2022-02-07 17:09:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/434#issuecomment-1031706517  

Ugh, even `#pragma GCC system_header` doesn't silence this one!

---

## Comment 8

> Username: jzmaddock  
> Created at: 2022-02-07 17:22:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/434#issuecomment-1031720475  

Well this is just crazy, if I replace the offending code with an actual call to memset (rather than let the compiler generate the call), then the warning disappears!  Testing changes locally now...

---

## Comment 9

> Username: ckormanyos  
> Created at: 2022-02-07 18:26:47 UTC  
> Updated at: 2022-02-07 18:28:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/434#issuecomment-1031781389  

> Well this is just crazy, if I replace the offending code with an actual call to memset (rather than let the compiler generate the call), then the warning disappears! Testing changes locally now  
  
Great!  
  
- Sorry for the late response. I confirm that something in the `max` function was seemingly related to this issue. I see that also on GCC 8 with a few (but not all) `-std=` settings.  
- I can confirm that I've actually used that workaround before. Yes. Use the legacy C Library functions. Uggghhhh. Sorry I forgot.  
  
Note to self when in doubt on this delightful genre of message, expand in C-Lib funcs.

---

## Comment 10

> Username: ckormanyos  
> Created at: 2022-02-07 19:36:27 UTC  
> Updated at: 2022-02-07 19:38:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/434#issuecomment-1031844153  

Thanks again , John (@jzmaddock). Great find. Great workaround!  
  
I can also confirm successful correction on `issue434` branch of a seemingly related phenomenon.  
  
For the sake of completeness the (and this is now fully corrected on 434), I received a compiler warning/note from the following:  
  
```sh  
g++-8 -std=c++11 -O3 -DNDEBUG -I/mnt/c/boost/modular_boost/boost/libs/multiprecision/include -I/mnt/c/boost/boost_1_78_0 test_arithmetic_cpp_bin_float_2m.cpp -o test_arithmetic_cpp_bin_float_2m.exe  
```  
  
This one was particularly confusing because no line number was given --- only internals, i.e., like this:  
  
```sh  
In static member function ‘static std::numeric_limits<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>, ExpressionTemplates> >::number_type std::numeric_limits<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>, ExpressionTemplates> >::max() [with unsigned int Digits = 500; boost::multiprecision::backends::digit_base_type DigitBase = (boost::multiprecision::backends::digit_base_type)10; Allocator = std::allocator<char>; Exponent = long long int; Exponent MinE = 0; Exponent MaxE = 0; boost::multiprecision::expression_template_option ExpressionTemplates = (boost::multiprecision::expression_template_option)1]’:  
cc1plus: warning: ‘void* __builtin_memset(void*, int, long unsigned int)’ writing 200 bytes into a region of size 32 overflows the destination  
-Wstringop-overflow=]  
```  
  
This warning/note is now completely absent with this GCC 8 test case on the `issue434`branch. Yeah!
