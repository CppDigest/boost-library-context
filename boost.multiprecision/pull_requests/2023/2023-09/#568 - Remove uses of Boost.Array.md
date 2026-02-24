# #568 Remove uses of Boost.Array [Merged]

> Username: mborland  
> Created at: 2023-09-22 06:55:08 UTC  
> Updated at: 2023-11-03 06:42:11 UTC  
> Merged at: 2023-11-03 06:42:05 UTC  
> Closed at: 2023-11-03 06:42:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/568  

Closes: #567

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2023-10-31 09:20:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/568#issuecomment-1786817871  

Not sure why this worked before... apparently boost::rational has no swap non-member, so we need a using std::swap in the test case.

---

## Comment 2

> Username: mborland  
> Created_at: 2023-11-02 07:18:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/568#issuecomment-1790195318  

@jzmaddock Any idea why the following now pops up in GNU mode?  
  
  
```  
../../../boost/math/special_functions/beta.hpp:573:35: error: call of overloaded 'log1p(boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<100>, (boost::multiprecision::expression_template_option)0>)' is ambiguous  
             power1 = exp(a * log1p((x * b - y * a) / a + p * (x * c / a)));  
```

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2023-11-02 09:25:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/568#issuecomment-1790363420  

That's weird, and new :(  
  
Not just gnu mode as the clang tests are failing too, I'll see if I can reproduce....

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2023-11-02 12:14:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/568#issuecomment-1790618300  

Fix here: https://github.com/boostorg/math/pull/1043

---

## Comment 5

> Username: mborland  
> Created_at: 2023-11-02 12:16:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/568#issuecomment-1790620733  

> Fix here: [boostorg/math#1043](https://github.com/boostorg/math/pull/1043)  
  
Thank you. Once that is merged I will re-run the CI here.

---
