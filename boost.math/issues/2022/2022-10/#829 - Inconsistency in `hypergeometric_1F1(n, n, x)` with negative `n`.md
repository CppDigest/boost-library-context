# #829 - Inconsistency in `hypergeometric_1F1(n, n, x)` with negative `n` [Closed]

> Username: WarrenWeckesser  
> Created at: 2022-10-03 22:52:27 UTC  
> Updated at: 2022-10-06 10:10:57 UTC  
> Closed at: 2022-10-06 10:10:57 UTC  
> Url: https://github.com/boostorg/math/issues/829  

According the [documentation of `hypergeometric_1F1`](https://www.boost.org/doc/libs/1_80_0/libs/math/doc/html/math_toolkit/hypergeometric/hypergeometric_1f1.html), *"This function has the same definition as Mathematica's Hypergeometric1F1[a, b, z]."*  There appears to be a difference in the case `a == b` and `a` is a negative integer.  I used the Wolfram Alpha website to check the Mathematica values, and I also checked `mpmath`.  The following shows the actual results that I get when computing ₁F₁(n, n, x) for the three libraries. Tn(x) is the degree n Taylor polynomial of exp(x) (i.e. T0(x) = 1, T1(x) = 1 + x, T2(x) = 1 + x + x**2/2, etc).  
  
```  
             Boost               Wolfram Alpha            mpmath  
   n   hypergeometric_1F1      Hypergeometric1F1       mpmath.hyp1f1  
   0          T0(x)                 T0(x)                  T0(x)  
  -1          T1(x)                 T1(x)                  T1(x)  
  -2          exp(x)                T2(x)                  T2(x)  
  -3          exp(x)                T3(x)                  T3(x)  
```  
  
Note that when `a` = `b` = `n` where `n` is a negative integer, two independent solutions to Kummer's equation are Tn(x) and exp(x).  The question then is, in this edge case, which one is considered *the* confluent hypergeometric function?  That is, which solution is the "first kind"?  Wolfram Alpha and mpmath consistently use the polynomial.  If the documentation is correct, then I would expect Boost to do the same.  If there is a compelling reason to use exp(x), I would expect that to apply in the cases n=0 and n=-1 also.  
  
I ran into this while converting SciPy's implementation of `hyp1f1` to use Boost as the backend. Currently SciPy uses a mix of some custom code and the Fortran library SPECFUN.  The current SciPy code also follows the convention of using the Taylor polynomial consistently.  
  
I also checked GSL (GNU Scientific Library).  It appears to use exp(x) in all the above cases except n = 0, where it generates a domain error.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-10-04 07:33:21 UTC  
> Url: https://github.com/boostorg/math/issues/829#issuecomment-1266526147  

Good question, unless there is a really compelling reason otherwise, I tend to assume that Mathematica is correct unless proven otherwise (just very occasionally they can be wrong, there are a lot of corner cases in some of the functions!).  Interesting that GSL goes the other way.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2022-10-04 07:43:06 UTC  
> Url: https://github.com/boostorg/math/issues/829#issuecomment-1266534797  

Looks like you have to be careful how you ask the question: https://www.wolframalpha.com/input?i=Hypergeometric1F1%5B-n%2C-n%2C+x%5D  
  
But then we have: https://functions.wolfram.com/HypergeometricFunctions/Hypergeometric1F1/03/01/04/02/

---

## Comment 3

> Username: ckormanyos  
> Created at: 2022-10-04 08:14:33 UTC  
> Url: https://github.com/boostorg/math/issues/829#issuecomment-1266567392  

Hi warren (@WarrenWeckesser)  
Hi John,  
  
I'm a bit new to this thread, but could we discuss some concrete examples in both Mathematica(R) as well as Boost? Are we talking about precision or simply the fundamental definition of the actual special function in this thread?  
  
I cranked up Mathematica 12.2 and found:  
  
```  
N[Hypergeometric1F1[-9, -9, 1/2], 20]  
  
1.6487212704182512125  
```  
  
Then I made the program in C++ using boost below. It agrees with Mathematica to within about 10 decimal digits.  
  
```  
#include <iostream>  
  
#include <boost/math/special_functions/hypergeometric_1F1.hpp>  
  
auto main() -> int  
{  
  using float_type = double;  
  
  const auto x = static_cast<float_type>(0.5L);  
  
  const auto h = boost::math::hypergeometric_1F1(-9, -9, x);  
  
  // 1.64872127070013  
  std::cout << std::setprecision(std::numeric_limits<float_type>::digits10)  
            << h  
            << std::endl;  
}  
```

---

## Comment 4

> Username: ckormanyos  
> Created at: 2022-10-04 08:40:38 UTC  
> Url: https://github.com/boostorg/math/issues/829#issuecomment-1266604172  

Also if you do not have Mathematica, then usually Wolfram Alpha uses the same conventions as does Mathematica.  
  
So you can do some spot checking at Wolfram Alpha such as [here](https://www.wolframalpha.com/input?i=N%5BHypergeometric1F1%5B-9%2C+-9%2C+1%2F2%5D%2C+20%5D).  
  
The entire URL for that particular function value is:  
  
```  
https://www.wolframalpha.com/input?i=N%5BHypergeometric1F1%5B-9%2C+-9%2C+1%2F2%5D%2C+20%5D  
```

---

## Comment 5

> Username: jzmaddock  
> Created at: 2022-10-04 12:22:00 UTC  
> Url: https://github.com/boostorg/math/issues/829#issuecomment-1266907375  

Chris, it's not a precision issue, it's a definition issue.  
  
We have a general result:  
  
1) 1F1[-a, -a, x] = e^x  
  
and a specific result:  
  
2) 1F1[-n, -n, x] = e^x Q[n+1, x] ; n an integer  
  
They are both legitimate results, so the question is which we should be using.  Note that Q[n+1,x] is so close to 1 as to make the two results identical except when n is small and x ~ 1.  
  
Using (1) has the advantage of making 1F1[a, a, x] a continuous function as you vary `a`, where as (2) makes it discontinuous.  However, (2) has a finite series representation (https://functions.wolfram.com/HypergeometricFunctions/Hypergeometric1F1/03/01/04/02/0003/) which is somewhat in keeping with the primary series definition (https://functions.wolfram.com/HypergeometricFunctions/Hypergeometric1F1/02/) - I say somewhat, because we would need to believe that `0 / 0` is 0!  On the other hand if we take the primary series and assume that (a)_n/(a)_n is always 1, even when a = 0, then we get (1).  
  
I guess on the basis that (2) is more specific than (1), and that it's what Mathematica does, we should probably use that?

---

## Comment 6

> Username: ckormanyos  
> Created at: 2022-10-04 12:49:53 UTC  
> Url: https://github.com/boostorg/math/issues/829#issuecomment-1266945652  

> I guess on the basis that (2) is more specific than (1), and that it's what Mathematica does, we should probably use that?  
  
Ahhh. Thanky for explaining, John.  
  
Now I get it. Sorry, my story going on about Wolfram Alpha was actually redundant with the OP and John's comments. I had overlooked those somewhat. I just looked at some of my work from 15 years ago, and I had just used the pure exponent series expansion near the origin, which seems to be the exact discrepancy we are talking about.  
  
The OP does seem like a very special case, but the discrepance is there. On a side note, I had also made the same interpretation (use exp(x)) years ago.  
  
It seems like we usually try to be consistent with number 2, the Mathematica way.

---

## Comment 7

> Username: WarrenWeckesser  
> Created at: 2022-10-04 13:47:27 UTC  
> Url: https://github.com/boostorg/math/issues/829#issuecomment-1267029186  

> Looks like you have to be careful how you ask the question: https://www.wolframalpha.com/input?i=Hypergeometric1F1%5B-n%2C-n%2C+x%5D  
  
It looks like even Wolfram Alpha gets a bit confused.  In that link, under *Result* it gives exp(x), but further down, under *Values*, it gives the formula for a few specific values of `n`, and those are the polynomials.

---

## Comment 8

> Username: WarrenWeckesser  
> Created at: 2022-10-04 14:09:48 UTC  
> Updated at: 2022-10-04 15:52:08 UTC  
> Url: https://github.com/boostorg/math/issues/829#issuecomment-1267067679  

> 2. 1F1[-n, -n, x] = e^x Q[n+1, x] ; n an integer  
  
I'm not fluent in all the relevant notation.  Do you have a link for the definition of Q?  
  
Even if the Boost implementation changes, I might have to handle this case explicitly in SciPy until we can rely on the updated Boost.  Is there anything in Boost that evaluates what I called Tn(x) efficiently?  It is "just" a polynomial, so there are several ways it could be evaluated, but I am wondering if there is something in the library that already handles this particular set of polynomials.

---

## Comment 9

> Username: jzmaddock  
> Created at: 2022-10-04 16:59:24 UTC  
> Url: https://github.com/boostorg/math/issues/829#issuecomment-1267295789  

>I'm not fluent in all the relevant notation. Do you have a link for the definition of Q?  
  
Incomplete Gamma Q: the same as boost::math::gamma_Q, though that only handles 0 <= x <= 1.  
  
>Even if the Boost implementation changes, I might have to handle this case explicitly in SciPy until we can rely on the updated Boost. Is there anything in Boost that evaluates what I called Tn(x) efficiently? It is "just" a polynomial, so there are several ways it could be evaluated, but I am wondering if there is something in the library that already handles this particular set of polynomials.  
  
Not really, the code ends up in `boost::math::detail::hypergeometric_pFq_checked_series_impl`, but you could also call `boost::math::hypergeometric_pFq` which is it's "public" API and will terminate when the numerator goes to zero.

---

## Comment 10

> Username: WarrenWeckesser  
> Created at: 2022-10-05 16:57:30 UTC  
> Url: https://github.com/boostorg/math/issues/829#issuecomment-1268691371  

Thanks @jzmaddock, calling `hypergeometric_pFq` to handle the special case works. (It also works to avoid the problem reported in https://github.com/boostorg/math/issues/833.)
