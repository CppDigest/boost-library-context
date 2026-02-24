# #506 - pow-function pow(scalar, complex) in boost/math/cstdfloat/cstdfloat_complex_std.hpp might get wrong result [Closed]

> Username: ckormanyos  
> Created at: 2021-01-30 13:56:37 UTC  
> Updated at: 2021-08-31 04:58:22 UTC  
> Closed at: 2021-08-31 04:58:22 UTC  
> Url: https://github.com/boostorg/math/issues/506  

At the board, it has been reported that  
pow-function pow(scalar, complex) in `boost/math/cstdfloat/cstdfloat_complex_std.hpp` get wrong result.  
  
Current implementation:  
  
```  
inline complex<BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE> pow(const BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE& x,  
                                                                const complex<BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE>& a)  
{  
  return std::exp(a * std::log(x));  
}  
```  
  
I think that's correct:  
  
```  
inline complex<BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE> pow(const BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE& x,  
                                                                const complex<BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE>& a)  
{  
  return std::exp(a * std::log(complex<BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE>(x)));  
}  
```

---

## Comment 1

> Username: ckormanyos  
> Created at: 2021-01-30 14:00:34 UTC  
> Updated at: 2021-01-30 14:03:55 UTC  
> Url: https://github.com/boostorg/math/issues/506#issuecomment-770216766  

It seems like the suggested syntax would, in fact, improve clarity of actually raising `x` real to the power of `a`complex. But I believe that the complex logarithm of `x` real is identical with the real-valued logarithm of `x `real. So the original cod, as it is written should, in fact, be expected to work.  
  
Maybe it's dependent on certain conditions.  
Is there a test case or compiler/code combination in this issue that fails?  
  
On VC14.2 x64, I tried the following trivial program below, and it gets what seems like the correct answer.  
  
```  
#include <iomanip>  
#include <iostream>  
  
#define BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE boost::multiprecision::cpp_dec_float_50  
  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
#include <boost/cstdfloat.hpp>  
#include <boost/math/cstdfloat/cstdfloat_complex_std.hpp>  
  
int main()  
{  
  using std::complex;  
  
  const boost::multiprecision::cpp_dec_float_50 xr = boost::multiprecision::cpp_dec_float_50(123U) / 100;  
  const complex<boost::multiprecision::cpp_dec_float_50> ac(boost::multiprecision::cpp_dec_float_50(456) / 100, boost::multiprecision::cpp_dec_float_50(789) / 100);  
  
  // N[(123/100)^((456/100) + ((789/100) I)), 51]  
  const complex<boost::multiprecision::cpp_dec_float_50> zp = pow(xr, ac);  
  
  // -0.16064972032257162271760752910062119061370111951926 + 2.56517670709240656144172160532701921161504478367641 I  
  std::cout << std::setprecision(std::numeric_limits<boost::multiprecision::cpp_dec_float_50>::digits10) << "zp: " << zp << std::endl;  
}  
```

---

## Comment 2

> Username: ckormanyos  
> Created at: 2021-01-30 14:07:19 UTC  
> Updated at: 2021-01-30 14:09:45 UTC  
> Url: https://github.com/boostorg/math/issues/506#issuecomment-770217779  

To be a bit more clear, the code above is intended to compute <img src="https://render.githubusercontent.com/render/math?math=1.23"> raised to the power <img src="https://render.githubusercontent.com/render/math?math=4.56">+<img src="https://render.githubusercontent.com/render/math?math=7.89I">, and it seems to get the right approximate answer to 50 digits.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2021-01-31 07:54:11 UTC  
> Updated at: 2021-01-31 07:55:15 UTC  
> Url: https://github.com/boostorg/math/issues/506#issuecomment-770342512  

> Is there a test case or compiler/code combination in this issue that fails?  
  
Yes, for real-valued negative argument `x` less than zero, the function returns `NaN`, but it should return a finite complex value. This has been shown at the Boost board.  
  
Many thanks for this good catch and your patient observation. I will fix this issue ASAP in a separate branch.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2021-01-31 13:43:54 UTC  
> Url: https://github.com/boostorg/math/issues/506#issuecomment-770384667  

My initial tendency is to correct this in the three regions of `x`:  
- for `x < 0`  
- for `x > 0`  
- neither, meaning `x = 0` in the sense of floating-point equality  
  
That correction looks something like this:  
  
```  
    inline complex<BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE> pow(const BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE& x,  
                                                                    const complex<BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE>& a)  
    {  
      complex<BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE> result;  
  
      if(x > BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE(0))  
      {  
        result = std::exp(a * std::log(x));  
      }  
      else if(x < BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE(0))  
      {  
        using std::atan2;  
        using std::fabs;  
        using std::log;  
  
        const complex<BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE> cpx_lg_x(log(fabs(x)), atan2(BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE(0), BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE(-1)));  
  
        result = std::exp(a * cpx_lg_x);  
      }  
      else  
      {  
        result =  
          complex<BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE>  
          (  
            -std::numeric_limits<BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE>::infinity(),  
            BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE(0)  
          );  
      }  
  
      return result;  
    }  
 ```

---

## Comment 5

> Username: ckormanyos  
> Created at: 2021-02-01 10:19:33 UTC  
> Url: https://github.com/boostorg/math/issues/506#issuecomment-770745051  

I put in a few zero, inf, NaN checks and added relevant tests.  
The actual coding on this fix should be about done now.

---

## Comment 6

> Username: ckormanyos  
> Created at: 2021-05-03 20:49:39 UTC  
> Url: https://github.com/boostorg/math/issues/506#issuecomment-831524956  

This issue is still open with some new and relevant suggestions from the board.

---

## Comment 7

> Username: ckormanyos  
> Created at: 2021-08-28 14:00:58 UTC  
> Updated at: 2021-08-30 12:27:39 UTC  
> Url: https://github.com/boostorg/math/issues/506#issuecomment-907630486  

It's been a while since this issue surfaced, but now I'll try to restart work on this issue.  
  
Summary of open points as far as I understand includes:  
  
- Check args 0, inf, NaN on functions like exp, pow, log, etc.  
- Generally check edge cases of function args.  
- Try to ensure that the set of complex functions is as complete as possible.  
- ~~Review the richness and scope of constructors and possibly sync these (if found missing) with standard.~~ (do not handle richness of constructors within this issue)  
- Provide more tests in general and tests for any changes incurrec by this issue.  
  
At the moment, do not handle `constexpr`-ness any more than already done. This can be addressed later.  
  
Cc: @jzmaddock
