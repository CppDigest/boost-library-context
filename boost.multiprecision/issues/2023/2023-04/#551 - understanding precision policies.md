# #551 - understanding precision policies [Open]

> Username: ofloveandhate  
> Created at: 2023-04-30 23:23:17 UTC  
> Updated at: 2023-05-03 18:35:08 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/551  

I'm still working on understanding the policies implemented for mixed precision arithmetic.  i'm at boost 1.81 as installed from homebrew on a mac M1.  
  
I have the following code that *I think* doesn't produce expected results.  Namely, I make two variables `x` and `y` at precision 50 and 60 respectively. Then I make a variable `z` at precision 70, and default precision is 70.  Then I compute `x*y` with the various policies.  For all of them, `z=x*y` leaves `z` with the precision it started with.    Specifically, I think that for all policies except `preserve_target_precision` the precision of `z` should be `60` after.  But in all cases the precision of `z` is the current default precision; the precision of the target does not depend on the precision of the operands.  
  
Is this the expected behaviour?  am I not correctly setting the policy?  I used [the doc page on variable precision](https://www.boost.org/doc/libs/1_82_0/libs/multiprecision/doc/html/boost_multiprecision/tut/variable.html) as my guide, and I think what i have is correct.  
  
---  
  
test code:  
  
  
```  
  
  
BOOST_AUTO_TEST_CASE(arithmetic_precision_existing_variable)  
{  
  
  
  
	mpfr_float::default_precision(50);  
	mpfr_float x("0.01234567890123456789012345678901234567890123456789");  
	BOOST_CHECK_EQUAL(x.precision(), 50);  
  
  
	mpfr_float::default_precision(60);  
	mpfr_float y("0.12345678901234567890123456789012345678901234567890123456789");  
	BOOST_CHECK_EQUAL(y.precision(), 60);  
  
  
	// make a variable at precision 70  
	mpfr_float::default_precision(70);  
	mpfr_float z1("0"), z2("0"), z3("0"), z4("0"), z5("0");  
  
  
  
	// then try to write into this existing variable with various policies  
  
  
	// All expressions are evaluated at the precision of the highest precision variable within the expression, and then rounded to the precision of the target variable upon assignment. The precision of other types (including related or component types - see preserve_component_precision/preserve_related_precision) contained within the expression are ignored. This option has the unfortunate side effect, that moves may become full deep copies.   
	mpfr_float::default_variable_precision_options(boost::multiprecision::variable_precision_options::preserve_target_precision);  
	  
	z1 = x*y;  
  
	BOOST_CHECK_EQUAL(x.precision(), 50);  
	BOOST_CHECK_EQUAL(y.precision(), 60);  
	BOOST_CHECK_EQUAL(z1.precision(), 70);  
	  
  
  
  
	// All expressions are evaluated at the precision of the highest precision variable within the expression, and that precision is preserved upon assignment. The precision of other types (including related or component types - see preserve_component_precision/preserve_related_precision) contained within the expression are ignored. Moves, are true moves not copies.   
	mpfr_float::default_variable_precision_options(boost::multiprecision::variable_precision_options::preserve_source_precision);  
	  
	z2 = x*y;  
  
	BOOST_CHECK_EQUAL(x.precision(), 50);  
	BOOST_CHECK_EQUAL(y.precision(), 60);  
	BOOST_CHECK_EQUAL(z2.precision(), 60);  
  
  
  
  
  
	//All expressions are evaluated at the precision of the highest precision variable within the expression, and that precision is preserved upon assignment. If the expression contains component types then these are also considered when calculating the precision of the expression. Component types are the types which make up the two components of the number when dealing with interval or complex numbers. They are the same type as Num::value_type. Moves, are true moves not copies.   
	mpfr_float::default_variable_precision_options(boost::multiprecision::variable_precision_options::preserve_component_precision);  
	  
	z3 = x*y;  
  
	BOOST_CHECK_EQUAL(x.precision(), 50);  
	BOOST_CHECK_EQUAL(y.precision(), 60);  
	BOOST_CHECK_EQUAL(z3.precision(), 60);  
  
  
  
  
  
  
	// All expressions are evaluated at the precision of the highest precision variable within the expression, and that precision is preserved upon assignment. If the expression contains component types then these are also considered when calculating the precision of the expression. In addition to component types, all related types are considered when evaluating the precision of the expression. Related types are considered to be instantiations of the same template, but with different parameters. So for example mpfr_float_100 would be a related type to mpfr_float, and all expressions containing an mpfr_float_100 variable would have at least 100 decimal digits of precision when evaluated as an mpfr_float expression. Moves, are true moves not copies.   
	mpfr_float::default_variable_precision_options(boost::multiprecision::variable_precision_options::preserve_related_precision);  
	  
	z4 = x*y;  
  
	BOOST_CHECK_EQUAL(x.precision(), 50);  
	BOOST_CHECK_EQUAL(y.precision(), 60);  
	BOOST_CHECK_EQUAL(z4.precision(), 60);  
  
  
  
  
  
  
  
	// All expressions are evaluated at the precision of the highest precision variable within the expression, and that precision is preserved upon assignment. In addition to component and related types, all types are considered when evaluating the precision of the expression. For example, if the expression contains an mpz_int, then the precision of the expression will be sufficient to store all of the digits in the integer unchanged. This option should generally be used with extreme caution, as it can easily cause unintentional precision inflation. Moves, are true moves not copies.   
	mpfr_float::default_variable_precision_options(boost::multiprecision::variable_precision_options::preserve_all_precision);  
	  
	z5 = x*y;  
  
	BOOST_CHECK_EQUAL(x.precision(), 50);  
	BOOST_CHECK_EQUAL(y.precision(), 60);  
	BOOST_CHECK_EQUAL(z5.precision(), 60);  
}  
  
```

---

## Comment 1

> Username: ofloveandhate  
> Created at: 2023-04-30 23:23:47 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/551#issuecomment-1529165526  

the tests for z2, z3, z4, and z5 all fail in the above code.

---

## Comment 2

> Username: ofloveandhate  
> Created at: 2023-04-30 23:33:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/551#issuecomment-1529167714  

This is a continuation of #308 , and in turn #75

---

## Comment 3

> Username: ofloveandhate  
> Created at: 2023-04-30 23:43:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/551#issuecomment-1529169450  

more testing reveals that if the default precision is less than the max of that of the two operands, we'll get the max precision of the two operands back.  
  
i think uniform precision environments are probably my goal, as anything else seems difficult to control.  i'll wait for someone to reply before posting more.   
  
thanks for your attention!!!  🚀

---

## Comment 4

> Username: jzmaddock  
> Created at: 2023-05-01 18:05:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/551#issuecomment-1530024918  

OK there is a bug, but not the one you think.  
  
How this should work is that `preserve_target_precision` preserves the precision of the LHS of the = sign and all the others preserve the precision of the RHS.  For example all the asserts in the following example pass for me:  
  
```  
   using namespace boost::multiprecision;  
   mpfr_float a{ 2 }, b{ 3 }, z;  
   a.precision(40);  
   b.precision(50);  
   z.precision(20);  
  
   mpfr_float::thread_default_variable_precision_options(boost::multiprecision::variable_precision_options::preserve_target_precision);  
   z = a * b;  
   std::cout << z.precision() << std::endl;  
   assert(z.precision() == 20);  
   mpfr_float::thread_default_variable_precision_options(boost::multiprecision::variable_precision_options::preserve_source_precision);  
   z = a * b;  
   assert(z.precision() == 50);  
  
   z.precision(60);  
  
   mpfr_float::thread_default_variable_precision_options(boost::multiprecision::variable_precision_options::preserve_target_precision);  
   z = a * b;  
   assert(z.precision() == 60);  
   mpfr_float::thread_default_variable_precision_options(boost::multiprecision::variable_precision_options::preserve_source_precision);  
   z = a * b;  
   assert(z.precision() == 50);  
```  
  
Note the call to `thread_default_variable_precision_options`.  Unfortunately here's the bug: `default_variable_precision_options` forgets to set the options for the current thread!!  So your calls are basically having no effect at all.  I'll fix this shortly.

---

## Comment 5

> Username: ofloveandhate  
> Created at: 2023-05-01 18:54:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/551#issuecomment-1530071744  

While I wait for a fix (and for it to get distributed), should I rely on the `thread*` functions?

---

## Comment 6

> Username: jzmaddock  
> Created at: 2023-05-02 07:52:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/551#issuecomment-1531036042  

Yes the thread_* functions are actually working!  
  
But of course, uniform precision is the easiest model, it just requires a fair bit of discipline.

---

## Comment 7

> Username: ofloveandhate  
> Created at: 2023-05-03 16:10:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/551#issuecomment-1533321865  

I have a related question.  When making a new variable and the default precision is not equal to that of any operand, what should be the precision of a new variable?  Specifically, for the following code  
  
```  
mpfr_float::thread_default_precision(50);  
mpfr_float x("0.01234567890123456789012345678901234567890123456789");  
  
mpfr_float::thread_default_precision(60);  
mpfr_float y("0.12345678901234567890123456789012345678901234567890123456789");  
BOOST_CHECK_EQUAL(y.precision(), 60);  
  
mpfr_float::thread_default_precision(30);  
mpfr_float z = x*y;   // what should be the precision of `z`, and for which policies?    
```  
  
what should the precision of z be?  Does it depend on the precision options?    
  
I think APPoT is meaningless in this scenario because the target doesn't exist, in which case the precision should be one of:  
* default precision, so 30  
* the precision of one of the operands on the RHS, so likely 60.

---

## Comment 8

> Username: jzmaddock  
> Created at: 2023-05-03 16:39:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/551#issuecomment-1533360481  

The *only* thing the current default precision is used for for the precision of a new variable.  
  
In the case of an expression, the expression is *always* evaluated at the precision of it's largest component (albeit which components are selected is determined by the policies when they're not all floats), the policies then determine what happens next - preservation of that precision via for example `preserve_source_precision` or rounding to the precision of the target variable via `preserve_target_precision`.  In this case the target variable is assumed to have current default precision: I don't see there should be any difference between:  
  
```  
mpfr_float z = a*b;  
```  
  
and  
  
```  
mpfr_float z;  
z = a*b;  
```

---

## Comment 9

> Username: ofloveandhate  
> Created at: 2023-05-03 18:35:08 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/551#issuecomment-1533518880  

Ok, thank you.  Your explanation was very helpful.
