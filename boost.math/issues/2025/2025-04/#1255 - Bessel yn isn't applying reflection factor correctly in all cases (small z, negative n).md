# #1255 - Bessel yn isn't applying reflection factor correctly in all cases (small z, negative n) [Closed]

> Username: gajjanag  
> Created at: 2025-04-10 13:38:49 UTC  
> Updated at: 2025-04-12 19:07:02 UTC  
> Closed at: 2025-04-12 17:09:02 UTC  
> Url: https://github.com/boostorg/math/issues/1255  

Hi.  
  
Noticed internally at Meta while trying to integrate Boost yn into some of our code.  
  
Can be reproduced as:  
Yn (negative n arguments) Take n == -1  
x= 0x1.03ebbp-128 , boost returns val = -0x1.41085ep+127  
  
While Mathematica gives an extremely large positive value here (roughly 0x1.41085e5d24b94p+127), showing that Boost gets the sign incorrect here, though the magnitude seems right.  
  
I believe the issue arises from https://github.com/boostorg/math/blob/529f3a759d83aa9437613666ea6293c9336d4069/include/boost/math/special_functions/detail/bessel_yn.hpp#L56, essentially the "small z" path does not incorporate the reflection "factor" before returning.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2025-04-10 15:47:39 UTC  
> Url: https://github.com/boostorg/math/issues/1255#issuecomment-2794313002  

Thanks for the report: I'm trying to work through some Math issues now, so I'll add this one to the list ;)

---

## Comment 2

> Username: ckormanyos  
> Created at: 2025-04-11 06:36:31 UTC  
> Updated at: 2025-04-11 06:40:29 UTC  
> Url: https://github.com/boostorg/math/issues/1255#issuecomment-2795973729  

>> trying to integrate Boost yn  
  
>> While Mathematica gives an extremely large positive value here (roughly 0x1.41085e5d24b94p+127), showing that Boost gets the sign incorrect here, though the magnitude seems right.  
  
> work through some Math issues now, so I'll add this one to the list  
  
Thanks for the report.  
  
If it is just reflection, I might be capable of handling and fixing it and adding test(s). Let me take a look.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2025-04-11 09:07:37 UTC  
> Url: https://github.com/boostorg/math/issues/1255#issuecomment-2796303764  

The issue has been reproduced locally. I'm providing test code.  
  
```cpp  
#include <boost/math/special_functions/bessel.hpp>  
  
#include <iomanip>  
#include <iostream>  
#include <sstream>  
  
auto main() -> int  
{  
  using float_type = float;  
  
  const float_type x { 0x1.03ebbp-128F };  
  
  const float_type ctrl { 0x1.41085ep+127F };  
  
  const float_type result = ::boost::math::cyl_neumann(-1, x);  
  
  std::stringstream strm { };  
  
  strm << std::setprecision(std::numeric_limits<float_type>::digits10) << "result: " << result << "\nctrl  : " << ctrl;  
  
  std::cout << strm.str() << std::endl;  
}  
```

---

## Comment 4

> Username: ckormanyos  
> Created at: 2025-04-12 07:57:05 UTC  
> Url: https://github.com/boostorg/math/issues/1255#issuecomment-2798722785  

Error reproduced at [https://godbolt.org/z/nMjrhMdvs](https://godbolt.org/z/nMjrhMdvs).

---

## Comment 5

> Username: ckormanyos  
> Created at: 2025-04-12 19:07:00 UTC  
> Url: https://github.com/boostorg/math/issues/1255#issuecomment-2798958815  

OK #1256 has cycled. The new tests pass and the lines are covered in CI on CodeCov. See image below.  
  
Thanks again @gajjanag for reporting this. It won't "officially" get into Boost until 1.89 around late July. But Modular-Boost has the fix.  
  
Cc: @jzmaddock and @mborland   
  
![Image](https://github.com/user-attachments/assets/c81deaca-ed6b-43b7-bf2f-baa3f56a5d27)
