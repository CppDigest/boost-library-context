# #909 - `boost::math::ellint_2(0.0)` becomes less accurate in boost 1.81 [Closed]

> Username: frederick-vs-ja  
> Created at: 2022-12-31 14:24:35 UTC  
> Updated at: 2023-01-05 21:57:27 UTC  
> Closed at: 2023-01-05 21:57:27 UTC  
> Url: https://github.com/boostorg/math/issues/909  

Source:  
```C++  
#include <format>  
#include <iostream>  
#include <numbers>  
  
#include <boost/math/special_functions/ellint_2.hpp>  
  
int main() {  
    std::cout << std::format("{}\n{}\n", std::numbers::pi / 2, boost::math::ellint_2(0.0));  
}  
```  
  
Output with MSVC STL and boost 1.81:  
```  
1.5707963267948966  
1.5707963267948963  
```  
  
In boost 1.80, the result of `boost::math::ellint_2(0.0)` was equal to `std::numbers::pi / 2`, which was more accurate.  
  
This issue was discovered when updating reference to boost-math in MSVC STL (microsoft/STL#3312).

---

## Comment 1

> Username: mborland  
> Created at: 2022-12-31 14:42:25 UTC  
> Url: https://github.com/boostorg/math/issues/909#issuecomment-1368224372  

There were a number of changes to the elliptic integrals in 1.81 [from this PR](https://github.com/boostorg/math/pull/815). If you look at the accuracy graphs the error across the domain has decreased. We can add an edge case for 0 unless you have a better answer @jzmaddock.

---

## Comment 2

> Username: NAThompson  
> Created at: 2022-12-31 16:16:25 UTC  
> Url: https://github.com/boostorg/math/issues/909#issuecomment-1368247672  

FWIW, I like to print using hexfloats for cases like this:  
  
```  
#include <iostream>  
#include <numbers>  
  
#include <boost/math/special_functions/ellint_2.hpp>  
  
int main() {  
    std::cout << std::hexfloat << std::numbers::pi/2 << "\n";  
    std::cout << boost::math::ellint_2(0.0) << "\n";  
}  
```  
  
The result:  
  
```  
0x1.921fb54442d18p+0  
0x1.921fb54442d17p+0  
```  
  
So only a single bit is different. It appears that the value of π is rounded down, making the elliptic integral value inaccurate by a little over 1ULP. I confess that I'm not too worried about this . . .

---

## Comment 3

> Username: jzmaddock  
> Created at: 2023-01-02 19:57:16 UTC  
> Url: https://github.com/boostorg/math/issues/909#issuecomment-1369172509  

OK some background and explanation is required here:  
  
As @mborland says, I did some work on the elliptic integrals based on "Fast computation of complete elliptic integrals and Jacobian elliptic functions", Celestial Mechanics and Dynamical Astronomy, April 2012.  These routines are accurate, and super-fast, but only when expanded inline (without inline expansion, you don't see an improvement).  And in order to ensure inline expansion, I had to do some very careful work to make sure the code contained nothing that would inhibit that: which basically meant no branch statements, although a `switch` was apparently OK.    
  
So.... in general, the new versions are faster and a little bit more accurate, but the cost is that some special case handling has gone, and yes that does mean an extra .5ulp error in this special case.  
  
Are you wrapping these as `extern "C"` functions?  If so the cost of the function call will greatly outweigh the cost of handling that special case if you wanted to handle it yourselves, or else just up the tolerance on the expected value for this case in your tests if you can live with it?  
  
I think from our point of view, the new routines are probably better for those applications that really need to hammer these functions over and over, so I would prefer to not "fix" this if possible.  
  
I am open to other thoughts though?

---

## Comment 4

> Username: CaseyCarter  
> Created at: 2023-01-05 21:55:38 UTC  
> Url: https://github.com/boostorg/math/issues/909#issuecomment-1372828122  

I'm satisfied that this is an expected behavior change and not a mistake. I suspect @frederick-vs-ja is as well from the thumbs-up on https://github.com/boostorg/math/issues/909#issuecomment-1369172509. I'm happy to bump the tolerance on the MSVC STL test case and call it a day. Thanks for the quick response!
