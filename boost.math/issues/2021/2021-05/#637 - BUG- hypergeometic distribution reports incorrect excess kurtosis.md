# #637 - BUG: hypergeometic distribution reports incorrect excess kurtosis [Closed]

> Username: mckib2  
> Created at: 2021-05-30 20:16:28 UTC  
> Updated at: 2021-06-04 14:39:08 UTC  
> Closed at: 2021-06-04 14:39:08 UTC  
> Url: https://github.com/boostorg/math/issues/637  

We have identified in https://github.com/scipy/scipy/pull/14153 that `boost::math::hypergeometric_distribution` does not report excess kurtosis as [commonly defined](https://en.wikipedia.org/wiki/Hypergeometric_distribution).  The formula given [in the docs](https://www.boost.org/doc/libs/1_49_0/libs/math/doc/sf_and_dist/html/math_toolkit/dist/dist_ref/dists/hypergeometric_dist.html) does not appear to be commonly used and is not equivalent to that used by Wikipedia or WolframAlpha.  If this is indeed an accepted formulation a reference and note about differences might be appropriate.  
  
Specifically, the current [excess kurtosis test](https://github.com/boostorg/math/blob/b2538faaf9802af8e856c603b9001e33db826676/test/test_hypergeometric_dist.cpp#L471) compares against a value with an unknown origin and does not match what [WolframAlpha returns](https://www.wolframalpha.com/input/?i=kurtosis%5BHypergeometricDistribution%5B50%2C+200%2C+500%5D%5D%2C+variance%5BHypergeometricDistribution%5B50%2C+200%2C+500%5D%5D).  All other functions appear to return the correct value, i.e. variance, mean, etc:  
```  
    n, M, N = 50, 200, 500 (as in boost::math::hypergeometric_distribution unit tests)  
  
    # mean, variance, etc. correct  
    wolfram variance: 10.8216...  
    boost variance: 10.8216...  
  
    # kurtosis differs  
    wolfram kurtosis: 2.96917...  
    boost kurtosis: 2.515548...  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-06-04 08:05:16 UTC  
> Url: https://github.com/boostorg/math/issues/637#issuecomment-854464759  

Confirmed, formulas helpfully given here: https://www.wolframalpha.com/input/?i=HypergeometricDistribution%5Br%2C+n%2C+N%5D

---

## Comment 2

> Username: pabristow  
> Created at: 2021-06-04 08:53:36 UTC  
> Url: https://github.com/boostorg/math/issues/637#issuecomment-854494905  

Confirmed at scipy/scipy#14153 (comment)  and a fix using the above expression translated into C++ has been produced and will be available int he next release.

---

## Comment 3

> Username: pabristow  
> Created at: 2021-06-04 14:39:08 UTC  
> Url: https://github.com/boostorg/math/issues/637#issuecomment-854779119  

Duplicate closed in favour of https://github.com/boostorg/math/issues/639 that I will fix after 15 Jun 2021.
