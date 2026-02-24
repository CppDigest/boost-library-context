# #60 - assigning complex.real() does nothing [Closed]

> Username: ofloveandhate  
> Created at: 2018-08-06 23:48:41 UTC  
> Updated at: 2018-08-11 20:20:26 UTC  
> Closed at: 2018-08-10 17:42:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/60  

here is some code, which is currently has unexpected results to me:  
```  
namespace bmp = boost::multiprecision;  
using mpfr_complex = bmp::number<mpc_complex_backend<0>, bmp::et_on >;  
  
mpfr_complex b;  
b.real() = 2;  
b.imag() = 4;  
std::cout<< "should be (2,4): " << b <<'\n';  
```  
but i'm getting just 0.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-08-07 18:36:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/60#issuecomment-411158060  

You're assigning to a temporary, so yes it does do nothing, and I'm surprised you didn't get a compiler warning at the very least.  
  
Use:  
```  
b.real(2);  
```  
If you want to set one component.

---

## Comment 2

> Username: ofloveandhate  
> Created at: 2018-08-07 19:03:54 UTC  
> Updated at: 2018-08-07 19:30:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/60#issuecomment-411166671  

ok, seems fair.  one can't assign to real or imag with std::complex -- it generates an error, in fact -- so fine.  
  
however, for a multiprecision complex number, it seems like at least it would be good for the .real() getter to return a const reference, rather than a copy, to help prevent unnecessary temporaries, unexpected precision when the default precision differs from a number under inspection, and for when i need to build an expression out of the real or imaginary part.  
  
this test passes:  
```  
mpfr_complex::default_precision(50);  
mpfr_float::default_precision(50);  
mpfr_complex c;  
BOOST_CHECK_EQUAL(c.real().precision(),50);  
mpfr_complex::default_precision(40);  
BOOST_CHECK_EQUAL(c.real().precision(),50);  
```  
  
but these two fail:  
  
```  
mpfr_complex::default_precision(50);  
mpfr_float::default_precision(50);  
mpfr_complex a;  
BOOST_CHECK_EQUAL(a.real().precision(),50);  
mpfr_complex::default_precision(40);  
mpfr_float::default_precision(40);  
BOOST_CHECK_EQUAL(a.real().precision(),50);  
  
mpfr_complex::default_precision(50);  
mpfr_float::default_precision(50);  
mpfr_complex b;  
BOOST_CHECK_EQUAL(b.real().precision(),50);  
mpfr_float::default_precision(40);  
BOOST_CHECK_EQUAL(b.real().precision(),50);  
```  
  
that is, returning a copy makes it impossible to tell what the precision is based on the real() and imag() getters.     
  
a related problem is that changing default precision of mpc is not sufficient -- one also has to change the real scalar's default precision, too.  it seems to me that they should co-vary.  
  
i'd love to hear your thoughts.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2018-08-08 16:56:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/60#issuecomment-411477282  

> but these two fail:  
  
That looks like a bug to me: the precision of the temporary copies returned should always match that of the complex number.  
  
BTW as a practical matter, we can't return a reference, since there's nothing to return a return a reference to - for example with `mpc_complex` we don't store `mpfr_float`'s internally, but just the `mpc_t`.  
  
Re-opening while I look at the precision issue...

---

## Comment 4

> Username: jzmaddock  
> Created at: 2018-08-08 19:04:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/60#issuecomment-411517364  

This should be fixed in the above commit - however handling of basic arithmetic is probably a mess if the default precision changes - it's not necessarily even clear to me what *should* happen in that situation so we basically just do whatever MPFR does.

---

## Comment 5

> Username: ofloveandhate  
> Created at: 2018-08-08 20:46:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/60#issuecomment-411546517  

ok.  i'll give it a run and report back.

---

## Comment 6

> Username: ofloveandhate  
> Created at: 2018-08-08 20:49:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/60#issuecomment-411547312  

my main methods keep some "golden" copies of numbers at some highest precision, and when an algorithm needs to do computations, they downsample into "working" copies, and I enforce uniformity of precision with of working copies along with default precision.  precision woes are, i'm convinced, part of the price that i pay for adaptive precision -- and it's totally worth it.    
  
thanks!

---

## Comment 7

> Username: ofloveandhate  
> Created at: 2018-08-08 22:30:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/60#issuecomment-411574041  

the changes yielded a new error (included in full):  
```  
In file included from test/classes/function_tree_test.cpp:39:  
In file included from ./include/bertini2/function_tree.hpp:36:  
In file included from ./include/bertini2/function_tree/node.hpp:56:  
In file included from ./include/bertini2/num_traits.hpp:39:  
In file included from ./include/bertini2/mpfr_complex.hpp:46:  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/mpc.hpp:85:13: error: use of undeclared  
      identifier 'mpc_set_precision'  
            mpc_set_precision(m_data, mpc_get_prec(o.m_data));  
            ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/mpc.hpp:283:9: note: in instantiation of  
      member function 'boost::multiprecision::backends::detail::mpc_complex_imp<0>::operator=' requested here  
      d = v;  
        ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/detail/number_compare.hpp:27:13: note:   
      in instantiation of function template specialization  
      'boost::multiprecision::backends::detail::mpc_complex_imp<0>::compare<boost::multiprecision::backends::mpc_complex_backend<0> >' requested  
      here  
   return a.compare(b) == 0;  
            ^  
/Users/brakeda/math_projects/bertini2/boost_mp_complex/multiprecision_clone/include/boost/multiprecision/detail/number_compare.hpp:211:12: note:   
      in instantiation of function template specialization  
      'boost::multiprecision::default_ops::eval_eq<boost::multiprecision::backends::mpc_complex_backend<0> >' requested here  
   return !eval_eq(a.backend(), b.backend());  
           ^  
test/classes/function_tree_test.cpp:148:29: note: in instantiation of function template specialization  
      'boost::multiprecision::operator!=<boost::multiprecision::backends::mpc_complex_backend<0>,  
      boost::multiprecision::expression_template_option::et_on, boost::multiprecision::backends::mpc_complex_backend<0>,  
      boost::multiprecision::expression_template_option::et_on>' requested here  
        BOOST_CHECK(x->Eval<mpfr>()!=mpfr(0,0));  
```

---

## Comment 8

> Username: jzmaddock  
> Created at: 2018-08-09 08:09:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/60#issuecomment-411674548  

CI is failing as well, pushed some fixes and extended CI testing to more backends.

---

## Comment 9

> Username: jzmaddock  
> Created at: 2018-08-09 10:36:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/60#issuecomment-411714593  

With regard to mixed arithmetic we have:  
  
* Copying or move-assignment copies the precision of the source.  
* Assignment keeps the precision of the target.  
  
As soon as you do mixed arithmetic, or call any function that results in an expression template, there are likely to be hidden temporaries created which will have the current default precision.

---

## Comment 10

> Username: ofloveandhate  
> Created at: 2018-08-09 16:36:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/60#issuecomment-411819945  

i am back to compiling.  thanks!

---

## Comment 11

> Username: ofloveandhate  
> Created at: 2018-08-09 16:42:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/60#issuecomment-411821839  

on precision:  i think that the commit changes the way that precision is carried through copies.  this test *was* passing:  
  
```  
	DefaultPrecision(50);  
  
	mpfr_float x("0.01234567890123456789012345678901234567890123456789");  
	BOOST_CHECK_EQUAL(x.precision(), 50);  
  
	DefaultPrecision(30);  
	mpfr_float y = pow(x,2);  
	BOOST_CHECK_EQUAL(y.precision(), 30);  
	  
  
	mpfr_float z = x;  
	BOOST_CHECK_EQUAL(z.precision(), 30);  
```  
and now it fails.  however, upon reading your previous comment  
  
> * Copying or move-assignment copies the precision of the source.  
> * Assignment keeps the precision of the target.  
  
i see that my test as written is inconsistent with this.  so, i think my test needs to change, as does my home-rolled complex type i was using, and will continue to use for users with earlier Boost versions than probably 1.69(?).   to be clear, i like what you said, and it's very sensible.  thanks for the clarification  
  
can i please request that some comments about precision in copies and assignment be added to the documentation to help users know what to expect, for both real and complex?

---

## Comment 12

> Username: jzmaddock  
> Created at: 2018-08-10 17:42:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/60#issuecomment-412154925  

Documented, CI passing, so closing down.

---

## Comment 13

> Username: ofloveandhate  
> Created at: 2018-08-11 20:20:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/60#issuecomment-412299260  

awesome, thanks!!!
