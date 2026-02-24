# #1194 - Lost error handling in tgamma [Closed]

> Username: jzmaddock  
> Created at: 2024-09-08 09:06:37 UTC  
> Updated at: 2024-09-09 16:54:56 UTC  
> Closed at: 2024-09-09 16:54:56 UTC  
> Url: https://github.com/boostorg/math/issues/1194  

The recent refactoring seems to have caused some error handling losses in tgamma, for example:  
  
```  
#include <boost/math/special_functions/gamma.hpp>  
  
int main()  
{  
   using c99_error_policy = ::boost::math::policies::policy<  
      ::boost::math::policies::domain_error< ::boost::math::policies::errno_on_error>,  
      ::boost::math::policies::pole_error< ::boost::math::policies::errno_on_error>,  
      ::boost::math::policies::overflow_error< ::boost::math::policies::errno_on_error>,  
      ::boost::math::policies::evaluation_error< ::boost::math::policies::errno_on_error>,  
      ::boost::math::policies::rounding_error< ::boost::math::policies::errno_on_error> >;  
  
   double val = -std::numeric_limits<double>::infinity();  
  
   val = boost::math::tgamma(val, c99_error_policy());  
   assert(errno == EDOM); // fails  
  
   val = std::numeric_limits<double>::quiet_NaN();  
   val = boost::math::tgamma(val, c99_error_policy());  
   assert(errno == EDOM); // fails  
  
   val = std::numeric_limits<double>::infinity();  
   val = boost::math::tgamma(val, c99_error_policy());  
   assert(errno == ERANGE); // OK  
  
   val = 0;  
   val = boost::math::tgamma(val, c99_error_policy());  
   assert(errno == EDOM); // OK  
  
   val = -2;  
   val = boost::math::tgamma(val, c99_error_policy());  
   assert(errno == EDOM); // OK  
  
   return 0;  
}  
```

---

## Comment 1

> Username: mborland  
> Created at: 2024-09-09 13:15:13 UTC  
> Updated at: 2024-09-09 13:23:51 UTC  
> Url: https://github.com/boostorg/math/issues/1194#issuecomment-2338097834  

I'll take a look. I'm pretty sure that the tgamma implementation function had a recursive case in it so it got separated into two functions.   
  
Edit: https://github.com/boostorg/math/commit/18ef483309516837cfc382e39385445fc65cd379
