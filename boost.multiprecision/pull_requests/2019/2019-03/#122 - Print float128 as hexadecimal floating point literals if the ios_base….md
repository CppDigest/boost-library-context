# #122 Print float128 as hexadecimal floating point literals if the ios_base… [Merged]

> Username: NAThompson  
> Created at: 2019-03-02 22:17:27 UTC  
> Updated at: 2019-04-12 16:56:44 UTC  
> Merged at: 2019-04-12 13:30:07 UTC  
> Closed at: 2019-04-12 13:30:07 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/122  

… flag floatfield is set [CI SKIP]  
  
1) The fmtflag for hexfloat should be tested with  
  
f & (floatfield)  
  
Not semantically precise, but ok.  
  
http://www.cplusplus.com/reference/ios/hexfloat/  
  
https://en.cppreference.com/w/cpp/io/ios_base/fmtflags  
  
2) This has been implemented in quadmath:  
  
See:   
https://gcc.gnu.org/onlinedocs/libquadmath.pdf  
  
page 10, which is the reference I used. I also increased the buffer size to match quadmath's documentation.  
  
The C++ standard says that you should ignore the 'digits' requested when printing as hexadecimal, which is what this does.  
  
3) I didn't see any print tests, so I just wrote a little MWE:  
  
```cpp  
#include <iostream>  
#include <boost/multiprecision/float128.hpp>  
#include <boost/math/constants/constants.hpp>  
  
using boost::multiprecision::float128;  
using boost::math::constants::pi;  
  
int main()  
{  
    std::cout << pi<float128>() << "\n";  
    std::cout << std::hexfloat;  
    std::cout << pi<float128>() << "\n";  
}  
```  
  
I would of course be glad to add a test for this; do you have any print tests?  
  
4) There are two paths for the .str() member of float128. I don't know how to test the other path since all my compilers hit the first path. But I believe this is an improvement on the status quo nonetheless.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2019-03-03 09:30:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/122#issuecomment-469004849  

>I would of course be glad to add a test for this; do you have any print tests?  
  
test_float_io.cpp is the main one - though cpp_bin_float has it's own separate loopback tests.  
  
>There are two paths for the .str() member of float128. I don't know how to test the other path since all my compilers hit the first path. But I believe this is an improvement on the status quo nonetheless.  
  
The #if logic?  The second branch is for intel's _Quad type which doesn't have a quadmath_snprintf function (or not last I checked).

---

## Comment 2

> Username: NAThompson  
> Created_at: 2019-03-03 17:41:35 UTC  
> Updated_at: 2019-03-03 17:42:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/122#issuecomment-469045830  

Looks like Intel does indeed work with quadmath_snprintf, at least that's what [this](https://software.intel.com/en-us/forums/intel-c-compiler/topic/749372) thread contends:  
  
> Using libquadmath (and quadmath_snprintf() to convert a quad-precision float to a string) is described here - https://gcc.gnu.org/onlinedocs/libquadmath/quadmath_005fsnprintf.html. See the example there. You can change it to use _Quad instead of __float128 but formatting and printing will still work with ICC. Don't forget to link with libquadmath (add -lquadmath) and add -Qoption,cpp,--extended_float_types to the command line to use _Quad.  
  
Until working with ICC gets a little easier, I'm not sure how interesting this is.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2019-03-06 17:53:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/122#issuecomment-470208338  

The CI failure was a network error, so this is ready to go.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2019-03-06 18:43:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/122#issuecomment-470226095  

Thanks Nick!  
  
I will probably wait until the release is out before merging in case there are any last minute urgent bug-fixes.  
  
So I guess your next challenge (only if you have the time) is to implement hexfloat formatting for the other binary floating point types: probably requires a pair of generic routines to read and write hexfloat format to/from an arbitrary floating point type.

---

## Comment 5

> Username: NAThompson  
> Created_at: 2019-03-06 19:03:01 UTC  
> Updated_at: 2019-03-06 19:11:06 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/122#issuecomment-470233366  

No problem not merging until after the release.  
  
I am interested in learning how to parse/print hexfloats in arbitrary precision, but in fact all I needed was to print float128s. So it does lower the urgency (for me) of getting the rest done.  
  
But I'll probably hit that issue someday, and write the rest then . . .  
  
This was super low-hanging fruit since quadmath already had it implemented.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2019-04-12 16:56:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/122#issuecomment-482647355  

Thanks for taking care of that one!

---
