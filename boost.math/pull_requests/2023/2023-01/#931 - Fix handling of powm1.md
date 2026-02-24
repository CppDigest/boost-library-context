# #931 Fix handling of powm1 [Closed]

> Username: mborland  
> Created at: 2023-01-27 23:48:40 UTC  
> Updated at: 2023-01-29 18:41:05 UTC  
> Closed at: 2023-01-29 18:41:04 UTC  
> Url: https://github.com/boostorg/math/pull/931  



---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2023-01-28 10:41:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/931#pullrequestreview-1273896995  

📁 test/scipy_issue_17870.cpp

```diff
  20 |+         
  21 |+         // See: https://en.cppreference.com/w/cpp/numeric/math/pow
  22 |+         if (x > 0) {
```

> Username: jzmaddock  
> Created_at: 2023-01-28 10:41:10 UTC  
> Url: https://github.com/boostorg/math/pull/931#discussion_r1089712097  

With a suitable policy all this logic is unneeded, for example with  
  
```  
#define BOOST_MATH_DOMAIN_ERROR_POLICY errno_on_error  
#define BOOST_MATH_OVERFLOW_ERROR_POLICY errno_on_error  
```  
  
Then no exceptions are thrown, and all the tests still pass (with MSVC anyway).

> Username: mborland  
> Created_at: 2023-01-28 16:31:48 UTC  
> Url: https://github.com/boostorg/math/pull/931#discussion_r1089771107  

I agree that that the macro for policy works. It sounds like they have had other policy based issues, and I do not know how macros propagate when compiled with all the binding mechanisms. The transpiled output is indecipherable. This is certainly less elegant, but I know it will work.

> Username: jzmaddock  
> Created_at: 2023-01-28 17:05:53 UTC  
> Updated_at: 2023-01-28 17:05:54 UTC  
> Url: https://github.com/boostorg/math/pull/931#discussion_r1089776802  

I guess the issue I have is that it's an awfully inefficient way to go for what is otherwise a rather simple/fast function.  Another alternative would be to define:  
  
```  
using c_style_policy = boost::math::policies::policy<boost::math::policies::overflow_error<boost::math::policies::errno_on_error>, boost::math::policies::domain_error<boost::math::policies::errno_on_error>>;  
```  
  
Then call via: `boost::math::powm1(x, y, c_style_policy());`  
  
Or else create a namespace scope policy:  
  
```  
namespace my_boost_math  
{  
   using namespace boost::math::policies;  
  
   // Define a policy that does not throw on overflow:  
   typedef policy<overflow_error<errno_on_error>, domain_error<errno_on_error> > c_style_policy;  
  
   // Define the special functions in this scope to use the policy:     
   BOOST_MATH_DECLARE_SPECIAL_FUNCTIONS(c_style_policy)  
}  
```  
  
And call via `my_boost_math::powm1(x, y)`  
  
In either case the function simply returns the expected values without any exceptions having to be raised.  
  
If they have a clear set of expectations for what values should be returned vs when exceptions are raised, it would be worth using the latter method in it's own header and using that consistently rather than adding lots of ad-hoc workarounds.


---
