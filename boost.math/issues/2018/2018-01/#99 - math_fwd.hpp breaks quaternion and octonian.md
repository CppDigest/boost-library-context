# #99 - math_fwd.hpp breaks quaternion and octonian [Closed]

> Username: swatanabe  
> Created at: 2018-01-10 18:26:21 UTC  
> Updated at: 2018-05-02 13:42:48 UTC  
> Closed at: 2018-01-30 08:28:09 UTC  
> Url: https://github.com/boostorg/math/issues/99  

```  
#include <boost/math_fwd.hpp>  
#include <boost/math/quaternion.hpp>  
  
int main() { boost::math::quaternion<double> x; }  
```  
  
test.cpp(4): error C2079: 'x' uses undefined class 'boost::math::quaternion<double>'

---

## Comment 1

> Username: thunder-blade  
> Created at: 2018-01-18 07:03:31 UTC  
> Url: https://github.com/boostorg/math/issues/99#issuecomment-358556539  

@swatanabe  it is not giving any error after excluding the math_fwd.hpp.

---

## Comment 2

> Username: swatanabe  
> Created at: 2018-01-28 21:15:33 UTC  
> Url: https://github.com/boostorg/math/issues/99#issuecomment-361096646  

AMDG  
  
On 01/28/2018 10:58 AM, Rakesh Senwar wrote:  
> @swatanabe   
> you should rearrange the first two lines->  
> #include <boost/math/quaternion.hpp>  
> #include <boost/math_fwd.hpp>  
>   
  
  This is a bug report, not a request for help  
in finding a workaround.  I already know how to  
work around the problem.  Also, this is a minimal  
test case.  In the original code, the two  
#includes were in totally separate places.  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: NAThompson  
> Created at: 2018-01-29 17:30:12 UTC  
> Url: https://github.com/boostorg/math/issues/99#issuecomment-361321814  

Looking at this code, I'm not sure the forward declarations were achieving much, as this file hasn't been touched since 2001. I simply replaced the forward declarations with includes, via:  
  
```  
#ifndef BOOST_MATH_FWD_HPP  
#define BOOST_MATH_FWD_HPP  
  
#include <boost/cstdint.hpp>  
#include <boost/math/quaternion.hpp>  
#include <boost/math/octonion.hpp>  
  
#endif  // BOOST_MATH_FWD_HPP  
```  
  
and everything works. Of course, presumably a deprecation warning might be useful for this file, unless John has a more sophisticated idea of what these forward declarations were intended to accomplish.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2018-01-30 08:28:05 UTC  
> Url: https://github.com/boostorg/math/issues/99#issuecomment-361512298  

Thanks for spotting that, fixed in https://github.com/boostorg/math/commit/426db8d929a6d68d3a5d7e7a42ae773345a72805.
