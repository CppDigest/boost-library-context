# #928 Fix for issue #927 [Closed]

> Username: mborland  
> Created at: 2023-01-25 17:53:42 UTC  
> Updated at: 2023-01-25 18:52:08 UTC  
> Closed at: 2023-01-25 18:47:29 UTC  
> Url: https://github.com/boostorg/math/pull/928  

Return `-INF` or `+INF` if the result is `INF` rather than throwing an overflow exception.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2023-01-25 18:02:15 UTC  
> Url: https://github.com/boostorg/math/pull/928#issuecomment-1404019580  

I think this is the wrong fix - and actually we are doing exactly what we promised to do here.  
  
If you want the sign of the infinity rather than an exception, then use a Policy which ignores overflow errors and you will get a signed infinity back.

---

## Comment 2

> Username: mborland  
> Created_at: 2023-01-25 18:04:47 UTC  
> Url: https://github.com/boostorg/math/pull/928#issuecomment-1404022684  

What is the appropriate policy to use here to get the signed infinity?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2023-01-25 18:14:55 UTC  
> Url: https://github.com/boostorg/math/pull/928#issuecomment-1404034184  

If it's project wide then   
  
```  
#define BOOST_MATH_OVERFLOW_ERROR_POLICY ignore_error  
```  
  
or  
  
```  
#define BOOST_MATH_OVERFLOW_ERROR_POLICY errno_on_error  
```  
  
Depending on whether you want to be able to check `errno` for overflow.  
  
If it's just this function, then:  
  
```  
using namespace boost::math::policies;  
  
boost::math::specfun(arg, make_policy(overflow_error<errno_on_error>()));  
```

---
