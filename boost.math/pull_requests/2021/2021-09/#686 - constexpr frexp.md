# #686 constexpr frexp [Merged]

> Username: mborland  
> Created at: 2021-09-03 05:43:00 UTC  
> Updated at: 2021-09-06 18:35:36 UTC  
> Merged at: 2021-09-06 18:27:27 UTC  
> Closed at: 2021-09-06 18:27:27 UTC  
> Url: https://github.com/boostorg/math/pull/686  

@ckormanyos This was borrowed heavily from your wide integer implementation with some added handling described in the standard (e.g. NaN). Do you have a sample `constexpr` test you use?  I am not sure how to get around `exp` having external visibility in a `constexpr` context. I could change to something like `frexp(T arg, void)`, but then the behavior would be different from the standard.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2021-09-03 07:49:06 UTC  
> Updated_at: 2021-09-03 07:49:34 UTC  
> Url: https://github.com/boostorg/math/pull/686#issuecomment-912333298  

> Do you have a sample `constexpr` test you use?  
  
Matt, thanks for pushing ahead on this function and the `constexpr` drive as an entire body of work.  
  
My tests only used my implementation within the context of larger `constexpr` algorithms. So I do not, per se, have any explicit tests of `constexpr` `frexp`.  
  
I am, however, convinced that the addess operator of the signed integer exponent argument (i.e., as in `double frexp(double, int*)`) rules out C++11 `constexpr`-ness entirely and requires `constexpr` from C++14 and higher standards. So this means that the word `constexpr` might not be allowed on that function when compiling with C++11. I was only able to achieve `constexpr`-ness on `frexp`-like and `ldexp`-like functions for C++14 or higher.  
  
I will need to look at your implementation @mborland more closely. Once a differentiation is made between C++11/14-and-beyond `constexpr`-ness, my first thought is to start with some _really_ simple examples such as the one below.  
  
```  
  // N[125/32, 30]  
  // 3.90625000000000000000000000000  
  
  int n;  
  
  // 0.976562500000000000000000000000 * 2^2  
  const double x = std::frexp(3.90625, &n);  
```  
  
Unlike the traditional code above, however, you could use the `constexpr` version and as a first step check if `x` can be created and queried within a `constexpr` context.

---

## Comment 2

> Username: mborland  
> Created_at: 2021-09-03 17:32:55 UTC  
> Url: https://github.com/boostorg/math/pull/686#issuecomment-912698150  

@ckormanyos This code is all written using C++17 so the definition of `constexpr` should not be an issue. The following trivial test yields `error: modification of ‘n’ is not a constant expression`  
  
```  
template <typename T>  
void test()  
{  
    int n;  
    constexpr T x = boost::math::ccmath::frexp(T(3.90625), &n);  
}  
```  
  
I am not entirely sure this error can be avoided.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-09-03 18:16:24 UTC  
> Url: https://github.com/boostorg/math/pull/686#issuecomment-912722733  

> I am not entirely sure this error can be avoided.  
  
You can't use the function in that manner: it would have to be within the body of a larger constexpr function.

---

## Comment 4

> Username: mborland  
> Created_at: 2021-09-06 12:35:24 UTC  
> Url: https://github.com/boostorg/math/pull/686#issuecomment-913615380  

Pending review this is clean on CI. Next in the queue is `ldexp` by request. Adding @NAThompson.

---

## Review 5 [Commented]

> Username: jzmaddock  
> Created_at: 2021-09-06 15:57:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/686#pullrequestreview-747335961  

📁 doc/sf/ccmath.qbk

```diff
  55 |+         inline constexpr Real frexp(Real arg, int* exp);
  56 |+ 
  57 |+         template <typename Z>
```

> Username: jzmaddock  
> Created_at: 2021-09-06 15:57:11 UTC  
> Url: https://github.com/boostorg/math/pull/686#discussion_r702990181  

Trivial quibble, but I when I first read this I didn't grok that this was limited to integer arguments, maybe use `<typename Integer>` and perhaps a comment that this is for integer arguments?  
  
Other than that, I'm happy for this to be merged, and you'll be pleased to know that ldexp should be noticeably easier! :)

> Username: NAThompson  
> Created_at: 2021-09-06 16:55:23 UTC  
> Url: https://github.com/boostorg/math/pull/686#discussion_r703011995  

@jzmaddock : That was my (perhaps poor) convention; mimicking ℤ.  
  
Is it possible to do a concept without breaking C++17 compatibility?

> Username: jzmaddock  
> Created_at: 2021-09-06 17:29:48 UTC  
> Url: https://github.com/boostorg/math/pull/686#discussion_r703021734  

Not really, although the enable_if does much the same thing.  It's debatable whether the enable_if condition should be part of the declared interface - it clutters up the docs, but does at least make it clear what the requirements on Z are.

> Username: mborland  
> Created_at: 2021-09-06 17:44:36 UTC  
> Url: https://github.com/boostorg/math/pull/686#discussion_r703025804  

Doc suggestions have been incorporated in the latest commit.  
  
As for concepts, you can avoid breaking C++17 compatibility using this macro (taken from [good concepts](https://www.stroustrup.com/good_concepts.pdf)):   
  
```  
#ifdef GOOD_COMPILER   
#define REQUIRES requires   
#elseif   
#define REQUIRES //   
#endif   
```


---
