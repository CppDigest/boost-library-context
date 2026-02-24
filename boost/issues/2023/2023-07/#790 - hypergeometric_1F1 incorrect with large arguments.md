# #790 - hypergeometric_1F1 incorrect with large arguments [Closed]

> Username: StatsMan53  
> Created at: 2023-07-11 14:32:51 UTC  
> Updated at: 2023-07-11 15:55:22 UTC  
> Closed at: 2023-07-11 15:55:22 UTC  
> Url: https://github.com/boostorg/boost/issues/790  

Through at least version 1.81.0, hypergeometric_1F1(1020,1041,16000) returns 1, when the correct answer overflows double precision. Wolfram Alpha fails here, but https://keisan.casio.com/exec/system/1349143651 reports it as 1.35e6923.  Since all inputs are positive with b>a, the result is equivalent to the integral from 0 to 1 of   exp(x*16000)*dbeta(x,1020,1041-1020), which integrand is nonnegative and overflows for x>0.1563122640622843, i.e. over 84% of the range of integration.  
  
This example is a test case from Keith Muller "Computing the confluent hypergeometric function, M(a,b,x)" Numerische Mathematik 90(1):179-196, 2001

---

## Comment 1

> Username: StatsMan53  
> Created at: 2023-07-11 15:55:22 UTC  
> Url: https://github.com/boostorg/boost/issues/790#issuecomment-1631082182  

Please disregard.  Problem was in my wrapper.
