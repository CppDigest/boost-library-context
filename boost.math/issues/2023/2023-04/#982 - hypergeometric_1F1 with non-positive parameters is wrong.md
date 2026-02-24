# #982 - hypergeometric_1F1 with non-positive parameters is wrong [Closed]

> Username: StatsMan53  
> Created at: 2023-04-28 14:00:44 UTC  
> Updated at: 2023-05-04 08:17:17 UTC  
> Closed at: 2023-05-04 08:17:17 UTC  
> Url: https://github.com/boostorg/math/issues/982  

hypergeometric_1F1(-2, -4, 2.5) returns 0.3609023, when the correct result is 2.770833 = 2+37/48  
hypergeometric_1F1(-2, -4, -2.5) returns 4.39669, when the correct result is 0.2708333 = 37/48  
  
In general, the result of hypergeometric_1F1(a,b,x) is suspect when b<=a<-1 are non-positive integers.  The code introduces a point discontinuity under these conditions, so a workaround is to average  
hypergeometric_1F1(a,b-epsilon,x) and hypergeometric_1F1(a,b+epsilon,x)

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-04-28 17:39:29 UTC  
> Url: https://github.com/boostorg/math/issues/982#issuecomment-1527885898  

With:  
  
```  
   std::cout << std::setprecision(16);  
   std::cout << boost::math::hypergeometric_1F1(-2, -4, 2.5) << std::endl;  
```  
  
I get 2.770833333333333 which agrees with wolfram alpha.  
  
For   
  
```  
   std::cout << boost::math::hypergeometric_1F1(-2, -4, -2.5) << std::endl;  
```  
  
I see 0.2274438503537196 which is indeed incorrect.  
  
The problem appears to be that Kummer's relation `M(a, b, z) = e^z M(b-a, b, -z)` appears not to hold in the specific case that **both** a and b are negative integers (https://www.wolframalpha.com/input?i=1F1%5B-2%2C+-4%2C+-2.5%5D+%3D+%28e%5E-2.5+*+1F1%5B-4--2%2C+-4%2C+2.5%5D%29).  Fixing that is easy enough, but I worry that I'm missing something, or that some of the other relations we use may also fail.... at present I'm struggling to find any mention of this in the literature, I wonder if you have any references?  I had thought that the truncation of the series that occurs when `b-a` is a negative integer was the issue, but Kummer's relation appears to work fine in the case that a,b are _not_ integers but b-a _is_.  Weird.
