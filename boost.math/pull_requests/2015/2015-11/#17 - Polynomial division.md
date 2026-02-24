# #17 Polynomial division [Merged]

> Username: jeremy-murphy  
> Created at: 2015-11-01 05:27:35 UTC  
> Updated at: 2015-12-13 12:06:00 UTC  
> Merged at: 2015-12-13 12:06:00 UTC  
> Closed at: 2015-12-13 12:06:00 UTC  
> Url: https://github.com/boostorg/math/pull/17  

OK there are a lot of changes but they fall into two categories:   
1. quotient and remainder for polynomials over a field and over a unique factorization domain,  
2. changes to existing operators to enforce the representation of zero.  
  
The functionality is complete and there are unit tests.  
  
Division algorithms are from Knuth, The Art of Computer Programming: Volume 2, Third edition, 1998:  
specifically, 4.6.1D (division of polynomials over a field) and 4.6.1R (pseudo-division of polynomials).  
  
By necessity, this also adds equality operators `==` and `!=` and for completeness a unary negate operator `-`.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-11-01 12:09:24 UTC  
> Updated_at: 2015-12-13 07:18:32 UTC  
> Url: https://github.com/boostorg/math/pull/17#discussion_r43581806  

If the size is zero, then the order is zero (with the order zero coefficient zero), so I think I'd prefer:  
  
if(empty()) return 0;  
  
rather than the assert.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2015-11-01 12:10:32 UTC  
> Updated_at: 2015-12-13 07:18:32 UTC  
> Url: https://github.com/boostorg/math/pull/17#discussion_r43581811  

Given that this will in effect be a new component, lets just go with C++11 here and use native lambda's and ditch the dependency on Boost.Lambda?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2015-11-01 12:13:15 UTC  
> Url: https://github.com/boostorg/math/pull/17#issuecomment-152822166  

Other than the suggestions above, looks fine, thanks, John.

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2015-11-01 23:03:20 UTC  
> Updated_at: 2015-12-13 07:18:32 UTC  
> Url: https://github.com/boostorg/math/pull/17#discussion_r43589519  

But that's the problem, the degree of the zero polynomial is not zero, it's undefined (http://mathworld.wolfram.com/ZeroPolynomial.html). By convention, some systems return -1 or -∞ but neither of those options are easy here. I thought an undefined value translated well to saying that this function is undefined when `size() == 0`. What about throwing an exception? Either way, I don't think users should expect `degree()` to return a useful value for the zero polynomial.

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2015-11-01 23:10:12 UTC  
> Updated_at: 2015-12-13 07:18:32 UTC  
> Url: https://github.com/boostorg/math/pull/17#discussion_r43589625  

Wouldn't that suddenly break the class for users compiling to '03, even if they're not using division? I'm definitely in favour of using newer language standards but I'm not sure if it's worth breaking backward compatibility just over one little lambda.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2015-11-02 09:24:26 UTC  
> Updated_at: 2015-12-13 07:18:32 UTC  
> Url: https://github.com/boostorg/math/pull/17#discussion_r43609166  

> But that's the problem, the degree of the zero polynomial is not zero,   
> it's undefined (http://mathworld.wolfram.com/ZeroPolynomial.html). By   
> convention, some systems return -1 or -∞ but neither of those options   
> are easy here. I thought an undefined value translated well to saying   
> that this function is undefined when |size() == 0|. What about   
> throwing an exception? Either way, I don't think users should expect   
> |degree()| to return a useful value for the zero polynomial.  
  
OK, an exception is better than an assert then - range_error seems to be   
the closest - or maybe just runtime_error?

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2015-11-02 09:25:22 UTC  
> Updated_at: 2015-12-13 07:18:32 UTC  
> Url: https://github.com/boostorg/math/pull/17#discussion_r43609234  

> Wouldn't that suddenly break the class for users compiling to '03,   
> even if they're not using division? I'm definitely in favour of using   
> newer language standards but I'm not sure if it's worth breaking   
> backward compatibility just over one little lambda.  
  
I guess I'm easy either way, there shouldn't really be many folks using   
the class at present though.

---

## Comment 8

> Username: jeremy-murphy  
> Created_at: 2015-11-02 11:50:10 UTC  
> Updated_at: 2015-12-13 07:18:32 UTC  
> Url: https://github.com/boostorg/math/pull/17#discussion_r43620541  

On thinking about it further, I would actually recommend against throwing an exception. If a user calls `degree()` on a zero polynomial, it's tantamount to calling `front()` on an empty `vector`: it's simply a logic bug. The user should be checking whether a polynomial is zero and, knowing the mathematics of polynomials, not asking what the degree of the zero polynomial is, just as we don't divide by zero and expect forgiveness. If it were to throw an exception, `logic_error` would be most appropriate, the user now has to check for that exception, which is probably less productive than if they properly checked for the zero polynomial value in the first place.  
  
Now, to counter my own argument: `size()` actually provides exactly the right interface because it's defined for all polynomials including our problematic friend the zero polynomial. We don't really need `degree()`, but we have it, so we can make it a bit inefficient with the reassurance that we can always use `size()` for the most consistent and efficient interface.  
  
So, I don't mind particularly, but I am inclined towards the first option because I think the second option fosters a lack of rigour in thinking about arithmetic. In the end, it's your call. :)

---

## Comment 9

> Username: jeremy-murphy  
> Created_at: 2015-11-02 12:36:22 UTC  
> Updated_at: 2015-12-13 07:18:32 UTC  
> Url: https://github.com/boostorg/math/pull/17#discussion_r43623861  

I also realize that I'm being slightly hypocritical, because I throw a `domain_error` in `quotient_remainder` for divide by zero. The same harsh logic above should apply here, but somehow it seemed OK to be kind in that case. Ultimately, I would stick to my guns of "user beware" in favour of efficiency and optionally have a less efficient interface that throws an exception.

---

## Comment 10

> Username: jeremy-murphy  
> Created_at: 2015-11-02 13:32:36 UTC  
> Updated_at: 2015-12-13 07:18:32 UTC  
> Url: https://github.com/boostorg/math/pull/17#discussion_r43627755  

A last thought before I go to bed: if we provide the most efficient implementation with the least safeguards, it can easily be wrapped in functions that offer more safeguards. Doing the opposite (removing safeguards for added efficiency) is non-trivial.

---

## Comment 11

> Username: jeremy-murphy  
> Created_at: 2015-11-04 01:33:12 UTC  
> Url: https://github.com/boostorg/math/pull/17#issuecomment-153542787  

By the way, I did slip unary minus (negate) in here for the sake of completeness and testing arithmetic relations.

---

## Comment 12

> Username: jeremy-murphy  
> Created_at: 2015-11-07 10:35:38 UTC  
> Updated_at: 2015-12-13 07:18:32 UTC  
> Url: https://github.com/boostorg/math/pull/17#discussion_r44210940  

If it's OK with you I'd rather keep it simple and not broach the question of C++1y at the moment because it opens so many questions. If/when we come to proposing a Boost.Polynomial library in its own right then I would definitely be in favour of C++11 as a minimum or base.

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2015-11-07 12:07:21 UTC  
> Updated_at: 2015-12-13 07:18:32 UTC  
> Url: https://github.com/boostorg/math/pull/17#discussion_r44211511  

> If it's OK with you I'd rather keep it simple and not broach the   
> question of C++1y at the moment because it opens so many questions.   
> If/when we come to proposing a Boost.Polynomial library in its own   
> right then I would definitely be in favour of C++11 as a minimum or base.  
  
That's fine, John.

---

## Comment 14

> Username: jeremy-murphy  
> Created_at: 2015-11-16 05:44:38 UTC  
> Url: https://github.com/boostorg/math/pull/17#issuecomment-156922257  

OK, I've made some substantial changes to the way division is implemented and I think that's almost all of the work done. So it's probably worth giving the whole thing a once-over again.  
  
It uses `enable_if`/`disable_if` to enable the correct division function depending on whether the coefficients are integer or not. This seemed to be the most natural boundary to separate on until we can really identify what is a _field_ versus a _unique factorization domain_.  
  
A couple of arithmetic relations unit tests fail for polynomials over integers. Not necessarily actual bugs, maybe I'm just trying to test something that we can't expect from integer polynomials. Certainly the case of `a * 0.5 == a / 2` is not something I would expect to work. Also, division of _reducible_ polynomials seems not to work as expected; I need to look into this a bit more to understand why.  
  
But yeah, apart from these curious unit test failures, I'm hoping that it's 99% done.

---

## Comment 15

> Username: jeremy-murphy  
> Created_at: 2015-11-18 01:22:10 UTC  
> Url: https://github.com/boostorg/math/pull/17#issuecomment-157566882  

OK, I fixed the failing test for `a * 0.5 == a / 2`, that was a bug from a combination of a) not exposing the heterogeneous division operators and b) the single-argument constructor not being explicit.  
  
Are you OK with that single-argument constructor being explicit now?

---

## Comment 16

> Username: jeremy-murphy  
> Created_at: 2015-11-18 01:23:45 UTC  
> Updated_at: 2015-12-13 07:18:32 UTC  
> Url: https://github.com/boostorg/math/pull/17#discussion_r45148201  

I might find a better algorithm for integer power, as I assume this is not ideal.

---

## Comment 17

> Username: jzmaddock  
> Created_at: 2015-11-18 19:22:04 UTC  
> Url: https://github.com/boostorg/math/pull/17#issuecomment-157829342  

> Are you OK with that single-argument constructor being explicit now?  
>   
> Seems not unreasonable to me.  John.

---

## Comment 18

> Username: jzmaddock  
> Created_at: 2015-11-18 19:24:36 UTC  
> Updated_at: 2015-12-13 07:18:32 UTC  
> Url: https://github.com/boostorg/math/pull/17#discussion_r45246617  

> I might find a better algorithm for integer power, as I assume this is   
> not ideal.  
  
Actually std::pow is generally pretty well optimized for that case - the   
"obvious" divide and conquer algorithm is often not much (or indeed any)   
faster.

---

## Comment 19

> Username: jeremy-murphy  
> Created_at: 2015-11-19 02:22:27 UTC  
> Updated_at: 2015-12-13 07:18:32 UTC  
> Url: https://github.com/boostorg/math/pull/17#discussion_r45292307  

OK, thanks, I'll leave it be.

---

## Comment 20

> Username: jeremy-murphy  
> Created_at: 2015-11-20 01:18:46 UTC  
> Url: https://github.com/boostorg/math/pull/17#issuecomment-158249690  

OK, fixed that bug. John, do you have any ad hoc regression tests you could run using the class to check I didn't break anything along the way?  
  
Otherwise, apart from maybe some more minor documentation updates, it's done.

---

## Comment 21

> Username: jeremy-murphy  
> Created_at: 2015-11-23 21:53:07 UTC  
> Url: https://github.com/boostorg/math/pull/17#issuecomment-159077326  

John, are there any more changes to the documentation or unit tests that you would like to see?

---

## Comment 22

> Username: jzmaddock  
> Created_at: 2015-11-24 08:36:09 UTC  
> Url: https://github.com/boostorg/math/pull/17#issuecomment-159195572  

On 23/11/2015 21:53, Jeremy W. Murphy wrote:  
  
> John, are there any more changes to the documentation or unit tests   
> that you would like to see?  
  
Sorry I haven't had a chance to look yet :(  Will do soon.... honest!

---

## Comment 23

> Username: jzmaddock  
> Created_at: 2015-11-27 18:22:06 UTC  
> Url: https://github.com/boostorg/math/pull/17#issuecomment-160185629  

This looks OK to me, however with MSVC I see:  
  
```  
test_polynomial.cpp(173): error C2666: 'boost::operator /': 2 overloads have similar conversions  
M:\data\boost\boost\boost/operators.hpp(252): note: could be 'boost::math::tools::polynomial<double> boost::operator /(T,const U &)' [found using argument-dependent lookup]  
        with  
        [  
            T=boost::math::tools::polynomial<double>,  
            U=double  
        ]  
M:\data\boost\boost\boost/operators.hpp(252): note: or       'boost::math::tools::polynomial<double> boost::operator /(T,const T &)' [found using argument-dependent lookup]  
        with  
        [  
            T=boost::math::tools::polynomial<double>  
        ]  
M:\data\boost\boost\boost/operators.hpp(252): note: or       'boost::math::tools::polynomial<int> boost::operator /(T,const U &)' [found using argument-dependent lookup]  
        with  
        [  
            T=boost::math::tools::polynomial<int>,  
            U=int  
        ]  
M:\data\boost\boost\boost/operators.hpp(252): note: or       'boost::math::tools::polynomial<int> boost::operator /(T,const T &)' [found using argument-dependent lookup]  
        with  
        [  
            T=boost::math::tools::polynomial<int>  
        ]  
test_polynomial.cpp(173): note: while trying to match the argument list '(const boost::math::tools::polynomial<double>, int)'  
test_polynomial.cpp(167): note: while compiling class template member function 'void test_arithmetic_relations<TestType>::test_method(void)'  
        with  
        [  
            TestType=double  
        ]  
test_polynomial.cpp(166): note: see reference to function template instantiation 'void test_arithmetic_relations<TestType>::test_method(void)' being compiled  
        with  
        [  
            TestType=double  
        ]  
test_polynomial.cpp(166): note: see reference to class template instantiation 'test_arithmetic_relations<TestType>' being compiled  
        with  
        [  
            TestType=double  
        ]  
M:\data\boost\boost\boost/test/tree/test_case_template.hpp(63): note: see reference to function template instantiation 'void test_arithmetic_relations_invoker::run<TestType>(boost::type<TestType> *)' being compiled  
        with  
        [  
            TestType=double  
        ]  
M:\data\boost\boost\boost/test/tree/test_case_template.hpp(63): note: while compiling class template member function 'void boost::unit_test::ut_detail::test_case_template_invoker<TestCaseTemplate,TestType>::operator ()(void)'  
        with  
        [  
            TestCaseTemplate=test_arithmetic_relations_invoker,  
            TestType=double  
```  
  
Any ideas?

---

## Comment 24

> Username: jeremy-murphy  
> Created_at: 2015-11-27 22:04:27 UTC  
> Url: https://github.com/boostorg/math/pull/17#issuecomment-160218452  

From what I understand [here](https://msdn.microsoft.com/en-us/library/dyafzty4.aspx), the compiler can't resolve such ambiguities and I had missed explicitly casting that one integer! I hope the commit I just pushed is all that was needed to fix it, but I'll try to get access to MSVC somehow for testing.

---

## Comment 25

> Username: jzmaddock  
> Created_at: 2015-11-28 09:55:47 UTC  
> Url: https://github.com/boostorg/math/pull/17#issuecomment-160270606  

On 27/11/2015 22:04, Jeremy W. Murphy wrote:  
  
> From what I understand here   
> https://msdn.microsoft.com/en-us/library/dyafzty4.aspx, the compiler   
> can't resolve such ambiguities and I had missed explicitly casting   
> that one integer! I hope the commit I just pushed is all that was   
> needed to fix it, but I'll try to get access to MSVC somehow for testing.  
  
Thanks, that gets it.  
  
I want to merge current develop to master one more time before release   
(after the beta is out), so I'll defer merging to master until then.    
Please do nag me if I forget!  
  
Thanks for this, John.

---
