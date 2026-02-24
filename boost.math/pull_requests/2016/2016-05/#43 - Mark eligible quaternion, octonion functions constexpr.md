# #43 Mark eligible quaternion, octonion functions constexpr [Closed]

> Username: kundor  
> Created at: 2016-05-26 01:42:41 UTC  
> Updated at: 2017-10-06 18:29:58 UTC  
> Closed at: 2017-10-06 18:29:58 UTC  
> Url: https://github.com/boostorg/math/pull/43  

It would be nice to be able to use the quaternion and octonion classes in constexpr functions.  
  
This marks all eligible functions as BOOST_CONSTEXPR or BOOST_CXX14_CONSTEXPR. Nothing else is changed (well, a couple typos in comments, but there are no code changes at all).  
  
If desired, I could probably make some small code changes to make some of the holdouts also constexpr: the explicit converting copy constructors could be modified not to call the helper function `detail::quaternion_type_converter` and then be constexpr.  
  
I checked the tests (`quaternion_mult_incl_test.cpp`, `quaternion_test.cpp`, and `octonion_test.cpp`) in GCC 4.3 with C++98 (no constexpr at all); GCC 5.3 with C++11 (strict constexpr support); GCC 6.1 with C++14 (relaxed constexpr support); Clang 600.0.57 with C++11; and Clang 703.0.31 with C++14. Everything passed.

---

## Comment 1

> Username: pabristow  
> Created_at: 2016-06-08 14:17:31 UTC  
> Url: https://github.com/boostorg/math/pull/43#issuecomment-224603288  

Looks a very good idea - and lots more of Boost.Math might benefit from enabling constexpr where possible?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2016-06-08 18:23:04 UTC  
> Url: https://github.com/boostorg/math/pull/43#issuecomment-224682765  

Also looks good to me, I have one nit-picking/stylistic comment and one substantive one:  
  
Stylistically, it looks really odd to me where you've placed the BOOST_CONSTEXPR macros - IMO qualifiers like this should appear immediately before the return type, ie:  
  
```  
template<typename X>     BOOST_CXX14_CONSTEXPR  
             octonion<T> &            operator += (octonion<X> const & rhs)  
```  
  
would look better as:  
  
```  
template<typename X>   
BOOST_CXX14_CONSTEXPR octonion<T> &            operator += (octonion<X> const & rhs)  
```  
  
The more substantive issue, is that I'd like to see some constexpr tests: past experience suggests that marking up alone and running "runtime" tests does not catch all the errors, you really do need to verify that the operators will function in a constexpr context.  And yes I realise that's quite a few tests to write!  
  
Oh and the docs need updating as well - sorry! ;)

---

## Comment 3

> Username: pabristow  
> Created_at: 2016-06-09 08:46:57 UTC  
> Url: https://github.com/boostorg/math/pull/43#issuecomment-224835488  

+1 for layout - BOOST_CONSTEXPR  position looks really, really odd to me too.  
  
I'll do any donkey work on the docs if that would help, but I'll wait for you to ask.    
  
A worked example (in /example) showing someconstexpr-ness would be a useful adddition to the docs, and might be mostly reused in a test in /test.  Demonstration of reduction in code size and/or speedup would be good too.  
  
(PS I'm looking at constexpr Bernoulli numbers using Chris Kormanyos fixed-point to implement mathy functions more efficiently).

---

## Comment 4

> Username: kundor  
> Created_at: 2016-06-09 15:27:18 UTC  
> Url: https://github.com/boostorg/math/pull/43#issuecomment-224931493  

Re the positioning—I put "BOOST_CONSTEXPR" etc. where I did to minimize rearranging what was already there. Often it doesn't fit in the whitespace before function names, and the original author seems to have had definite ideas about alignment which aren't entirely clear to me.  
  
Also, I didn't want to make it impossible to find the return type, which AFAIK is also the reason for putting `template <typename X>` etc. on a separate line.  
  
But I can put it before the return type if that's preferred.  
  
It will take me a little time to get around to adding tests and docs.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2017-10-06 18:29:58 UTC  
> Url: https://github.com/boostorg/math/pull/43#issuecomment-334834943  

See https://github.com/boostorg/math/pull/90

---
