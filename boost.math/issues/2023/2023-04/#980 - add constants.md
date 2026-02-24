# #980 - add constants [Closed]

> Username: gpeterhoff  
> Created at: 2023-04-26 18:44:14 UTC  
> Updated at: 2024-02-05 09:31:27 UTC  
> Closed at: 2024-02-05 09:31:27 UTC  
> Url: https://github.com/boostorg/math/issues/980  

Can you please add 2 constants to boost::math::constants:  
root_five = sqrt(5)  
psi = 1 - phi  
These are needed e.g. for the calculation of Fibonacci numbers.  
  
thx  
Gero

---

## Comment 1

> Username: mborland  
> Created at: 2023-04-27 09:57:04 UTC  
> Url: https://github.com/boostorg/math/issues/980#issuecomment-1525326705  

The value of [psi](https://en.wikipedia.org/wiki/Reciprocal_Fibonacci_constant) is available as `reciprocal_fibonacci`. I can add `root_five`. We also have `<boost/math/special_functions/fibonacci.hpp>` if that's what you need.

---

## Comment 2

> Username: gpeterhoff  
> Created at: 2023-04-27 10:30:53 UTC  
> Url: https://github.com/boostorg/math/issues/980#issuecomment-1525414983  

I meant this [psi](https://en.wikipedia.org/wiki/Fibonacci_sequence#Closed-form_expression) = -0.618033988749...
