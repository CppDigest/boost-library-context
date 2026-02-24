# #308 - variable precision arithmetic between complex and rational yields incorrect precision [Closed]

> Username: ofloveandhate  
> Created at: 2021-03-17 19:54:57 UTC  
> Updated at: 2021-07-29 16:53:35 UTC  
> Closed at: 2021-07-29 16:53:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/308  

I'm using Boost 1.75, via Homebrew.  I have a test failing in my code, because the precision of the result of mixed-type arithmetic (multiplication at least) between complex numbers and rational numbers is incorrect.  Code for test:  
  
```  
#include <boost/test/unit_test.hpp>  
#include <boost/multiprecision/mpc.hpp>  
  
  
BOOST_AUTO_TEST_SUITE(boost_multiprecision)  
BOOST_AUTO_TEST_CASE(precision_complex_rational_mul)  
{  
	using mpq_rational = boost::multiprecision::number<boost::multiprecision::backends::gmp_rational, boost::multiprecision::et_on>;  
	using mpc_complex = boost::multiprecision::number<boost::multiprecision::backends::mpc_complex_backend<0>, boost::multiprecision::et_on>;  
  
	mpc_complex::default_precision(30);  
  
	mpq_rational a(1,2);  
	mpc_complex b(0,1);  
  
	mpc_complex c = a*b;  
  
	BOOST_CHECK_EQUAL(c.precision(),30);  
}  
  
  
BOOST_AUTO_TEST_SUITE_END() // numtraits tests  
```  
  
The precision of `c` should be 30 -- it's coming to me as 646392383.

---

## Comment 1

> Username: ofloveandhate  
> Created at: 2021-03-17 19:59:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/308#issuecomment-801385307  

Same for all four primary arithmetic operations -- +-*/, with operands in either order.

---

## Comment 2

> Username: ofloveandhate  
> Created at: 2021-03-17 20:04:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/308#issuecomment-801395122  

This follows on #75

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-03-22 13:32:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/308#issuecomment-804063868  

Confirmed.  
  
I have a partial fix, but further testing reveals a whole swathe of situations where the current situation breaks down.  
  
To recap, we have assignment-preserves-precision-of-source.  And this should all be working now for same-type operations.  
  
However, things get screwy with integer and rational types.  The question is basically this: if I assign an integer to a variable precision floating point (or complex) type, then what is the precision of the result?  Options are:  
  
Variable precision integers:  
1) Sufficient precision to hold all the bits set in the integer.  
2) As (1), but never less than current default precision.  
3) Current default precision.  
  
Option (1) is a non-starter as this fails catastrophically:  
  
```  
float_type one(1), ten(10).  
float_type point_one = one / ten;  // one decimal digit precision!!  
```  
  
We currently do (2).  
  
For fixed precision integers:  
1) Sufficient precision to hold all the bits set in the integer.  
2) The precision (digits10) of the integer.  
3) As (1), but never less than current default precision.  
4) As (2), but never less than current default precision.  
5) Current default precision.  
  
1 and 2 are non starters as before, we're currently somewhere around (4), modulo any bugs.  For consistency, I'm considering (3).  
  
Rationals:  
It's not at all clear to me what we do here, except possibly to consider the larger of the precision (however we measure it above) of the 2 components, plus current default.  There is probably a stronger case to stick to current default precision here given that the result may well be irrational anyway.

---

## Comment 4

> Username: ofloveandhate  
> Created at: 2021-03-24 01:26:03 UTC  
> Updated at: 2021-03-24 01:27:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/308#issuecomment-805400955  

I wanted to share a few of my own thoughts about this issue.    
  
My codebase relies mostly on floating point calculations, with some values which have fixed values held as integers/rationals/very high precision numbers, which we down-sample from into floats.  So, for me, arithmetic between mixed arbitrary precision ints and rationals is not the highest priority.  
  
My workaround for this issue is to always downsample to floats.  But I lack a systematic way of preventing mixed-type arithmetic at this time.  I'd be content with having a way to compute `rational*float` and `int*float`, and get a float back at current default precision.  That would prevent me from having to write downsampling code wherever these mixed-type computations happen in my code.

---

## Comment 5

> Username: anilmuthigi  
> Created at: 2021-03-29 02:19:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/308#issuecomment-809020329  

Do you think that including something like a type caste operator(in order to specify the output precision you are looking for) in the documentation could be a good fix here? Something like this:  
  
	mpc_complex::default_precision(30);  
  
	mpq_rational a(1,2);  
	mpc_complex b(0,1);  
  
	mpc_complex c = (mpc_complex::setprecision(30))(a*b);

---

## Comment 6

> Username: jzmaddock  
> Created at: 2021-03-29 08:14:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/308#issuecomment-809172369  

>Do you think that including something like a type caste operator(in order to specify the output precision you are looking for) in the documentation could be a good fix here?  
  
Yes, definitely, but there are other issues to work through as well to get everything consistent.... more on that soon.  
  
In the meantime, something like this:  
  
```  
mpc_complex::default_precision(30);  
  
mpq_rational a(1,2);  
mpc_complex b(0,1);  
  
mpc_complex c = mpfr_float(a, 30) * b;  
```  
  
Works right now.  The catch is that the 2-arg constructor means something else for a complex number, so you need to construct the corresponding scalar type.

---

## Comment 7

> Username: ofloveandhate  
> Created at: 2021-03-29 15:38:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/308#issuecomment-809482595  

I'm struggling with this conversion.  I'd like to explain a bit.  
  
My code is templated on scalar type, and I call the type `RealT` -- it's probably `mpfr_float` or `double`, but could be any non-integral numeric type.  So, I have to take the real rational number I want to multiply by, and convert it to a `RealT`.  The constructor for `double` takes only one argument, but for `mpfr_float`, I have to pass in two, the second indicating the precision of the result (else it's ludicrous and everything downstream of it is polluted).    Thus, `RealT(myrational,current_precision)` is not workable.  
  
I'm wondering if you have any suggestions for how to deal with this, in the paradigm of APPoS, where construction of a variable-precision number will be very high, constructing from integral and rational types.

---

## Comment 8

> Username: jzmaddock  
> Created at: 2021-03-29 18:01:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/308#issuecomment-809589686  

Well... I never said the constructor was a _good_ solution: like you say, it's not generic.  
  
I'm contemplating in addition to a ::default_precision() member, adding some member flags that control this behaviour.  The alternative is a non-member cast operator, but then this is Boost.Multiprecision specific, where as a flag would be set at the same time as you set precision, before any generic code is called.  As a stopgap, if you have control of the generic portion of the code, then there's nothing to stop you from writing a cast operator now - the default version would do nothing, then overload for variable precision Multiprecision types.

---

## Comment 9

> Username: ofloveandhate  
> Created at: 2021-03-29 18:56:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/308#issuecomment-809629666  

Ah, gotcha.  I use my own `NumTraits<T>`, and I think I would add the cast in there.  I already use it for things like `Precision(RealT)`, which gives me a universal interface between all my numeric types.  Thanks for the nudge.

---

## Comment 10

> Username: ofloveandhate  
> Created at: 2021-04-21 01:21:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/308#issuecomment-823706608  

I see you working on this issue!  I'd love to test out the code when you get it to such a ready state!

---

## Comment 11

> Username: ofloveandhate  
> Created at: 2021-04-21 03:08:25 UTC  
> Updated at: 2021-04-21 03:09:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/308#issuecomment-823745002  

Here's an interesting currently-failing test case I just found:  
```  
BOOST_AUTO_TEST_CASE(precision_complex_longlong_div_set_float16_complex20)  
{  
	mpfr_float::default_precision(16);  
	mpc_complex::default_precision(20);  
  
	mpc_complex b(1,0);  
	unsigned long long d{13};  
	mpc_complex result{b/d};  
  
	BOOST_CHECK_EQUAL(result.precision(),20);  
}  
```  
  
The output I am getting:  
  
```  
error: in "boost_multiprecision/precision_complex_longlong_div_set_float16_complex20": check result.precision() == 20 has failed [19 != 20]  
```  
I think I should get a number of precision 20 out of this, not 19.  
  
  
Interestingly, I observe that this test case passes:  
```  
BOOST_AUTO_TEST_CASE(precision_complex_longlong_div_set_float20_complex20)  
{  
	mpfr_float::default_precision(20);  
	mpc_complex::default_precision(20);  
  
	mpc_complex b(1,0);  
	unsigned long long d{13};  
	mpc_complex result{b/d};  
  
	BOOST_CHECK_EQUAL(result.precision(),20);  
}  
```  
  
I think that I'm concluding that the precisions for the two backends need to be coupled?    
  
---  
  
From this test case, which also fails, I can draw another conclusion.  Test:  
```  
BOOST_AUTO_TEST_CASE(precision_complex_longlong_div_set_float20_complex16)  
{  
	mpfr_float::default_precision(20);  
	mpc_complex::default_precision(16);  
  
	mpc_complex b(1,0);  
	unsigned long long d{13};  
	mpc_complex result{b/d};  
  
	BOOST_CHECK_EQUAL(result.precision(),16);  
}  
```  
  
Output:  
```  
error: in "boost_multiprecision/precision_complex_longlong_div_set_float20_complex16": check result.precision() == 16 has failed [20 != 16]  
```  
  
My conclusion is that for these cases, it's `mpfr_float::default_precision()` which controls the precision of the resulting **complex** variable.  There's an `unsigned long long` here, which causes precision-19 numbers to be made, which is leading to code bloat everywhere I evaluate with multiplication and division by integers.    
  
I do hope this is addressed in the work you're doing!  I'm excited to have a more predictable world of default precisions.  Thanks!

---

## Comment 12

> Username: jzmaddock  
> Created at: 2021-04-21 08:13:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/308#issuecomment-823871850  

Thanks, those are plainly absurd, I'm slowly working through the permutations, but I haven't got to mixed types yet.... that's next!

---

## Comment 13

> Username: ofloveandhate  
> Created at: 2021-04-21 14:50:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/308#issuecomment-824123608  

I'll note that I didn't find this until now because I hadn't tested `mpc_complex` at precision 16 until now (tracing back some mysterious 19's popping up in a class method).  One might argue that I shouldn't be using precision 16 in a variable precision type, that I should be using `double`s for this.  But this was a curious edge case that I found, and I figured I would report it.

---

## Comment 14

> Username: ofloveandhate  
> Created at: 2021-04-21 14:50:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/308#issuecomment-824123702  

Will any of the behaviour you're implementing be adjustable at compile time?

---

## Comment 15

> Username: jzmaddock  
> Created at: 2021-04-21 17:32:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/308#issuecomment-824234749  

>Will any of the behaviour you're implementing be adjustable at compile time?  
  
At present I'm focused on runtime.  But it's not lost on me that some of the functions could be made constexpr if the user was willing to restrict the runtime options.

---

## Comment 16

> Username: ofloveandhate  
> Created at: 2021-04-24 15:38:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/308#issuecomment-826110634  

I finally ran into the boundary of where manual type conversion will let me work around this bug -- my Boost.Python code.  I enable arithmetic of mixed numeric types using visitor pattern and tons of templates, and to manually write the code exposing arithmetic between mpq_rational and mpc_complex is prohibitive.  I guess I'm saying "I'm grateful you're working on this, because my project needs it".  Thanks!

---

## Comment 17

> Username: jzmaddock  
> Created at: 2021-07-29 16:53:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/308#issuecomment-889306255  

Should now be addressed in develop
