# #254 - Division by zero [Closed]

> Username: Bouncner  
> Created at: 2019-09-20 09:49:16 UTC  
> Updated at: 2019-09-21 17:02:53 UTC  
> Closed at: 2019-09-21 17:02:53 UTC  
> Url: https://github.com/boostorg/math/issues/254  

I am using the skew-normal distribution of boost to generate random values.  
  
As noted in the documentation (`skew_normal_distribution(RealType location = 0, RealType scale = 1, RealType shape = 0);`), we use a shape of 0.0. Now we found that Clang's sanitiser finds an undefined behaviour due to a division by 0.  
  
``/usr/include/boost/math/distributions/skew_normal.hpp:290:106: runtime error: division by zero  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /usr/include/boost/math/distributions/skew_normal.hpp:290:106 in   
``  
  
The according line is `const RealType delta2 = static_cast<RealType>(1) / (static_cast<RealType>(1)+static_cast<RealType>(1)/(dist.shape()*dist.shape()));`.  
  
Am I being totally blind or does the default value for shape lead to a division by zero?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-09-20 19:36:47 UTC  
> Url: https://github.com/boostorg/math/issues/254#issuecomment-533683523  

>Am I being totally blind or does the default value for shape lead to a division by zero?  
  
No you're correct I think - it all comes out right in the end provided the floating point type has an infinity - but none the less I think we need to fix this.  
  
BTW the other moments appear to have the same issue not just the variance.
