# #63 - copy construction of mpc from mpfr_float doesn't preserve precision [Closed]

> Username: ofloveandhate  
> Created at: 2018-08-13 18:13:44 UTC  
> Updated at: 2018-08-17 21:46:43 UTC  
> Closed at: 2018-08-17 21:46:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/63  

i have a failing test case for `mpc`, which I think should pass:  
  
```  
// failing, z gets precision 50, not 100.  
BOOST_AUTO_TEST_CASE(construct_from_nondefault_reals)  
{  
	DefaultPrecision(100);  
	mpfr_float a(1);  
	mpfr_float b(2);  
  
	DefaultPrecision(50);  
  
	mpfr_complex z(a,b);  
  
	BOOST_CHECK_EQUAL(z.precision(), 100);  
}  
```  
  
based on what we worked through together in #60 with respect to precision, copy should preserve precision of the source.  in this case the source isn't a complex, but a real-imag pair.  but, precision is not preserved through copy.    
  
my focus in this blob of issues is random number generation, of complexes at some specified precision which is not the current default.  i can currently generate reals at any precision (it's not elegant at all, with some ugly if/elseif into a function templated on the desired precision), but complexes are proving difficult to make at not-default precision without just calling `default_precision` everywhere, which I want to avoid if possible.  this issue is also related to setters for the real and imag part -- if i had a real and imag setter, i think i could accomplish this goal easier.  thanks for the attention!!!

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-08-15 17:36:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/63#issuecomment-413274855  

Yup, I fixed this as part of #62.

---

## Comment 2

> Username: ofloveandhate  
> Created at: 2018-08-16 15:05:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/63#issuecomment-413577939  

this test is still failing for me.  can you help me diagnose it?  i am osx,  
```  
clang --version  
Apple LLVM version 9.0.0 (clang-900.0.39.2)  
Target: x86_64-apple-darwin16.7.0  
Thread model: posix  
```

---

## Comment 3

> Username: ofloveandhate  
> Created at: 2018-08-16 15:14:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/63#issuecomment-413581169  

or, do you mean that i should specify the precision when i am building these numbers, so that the complex gets made at the correct precision -- which is **not** inferred from the real and imaginary arguments?  if so, this should be documented, as i feel the behaviour differs from the copy constructor from complex, which does preserve precision.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2018-08-16 18:21:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/63#issuecomment-413639469  

Reproduced now...

---

## Comment 5

> Username: jzmaddock  
> Created at: 2018-08-17 08:10:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/63#issuecomment-413791586  

Can you please try again, should be fixed.

---

## Comment 6

> Username: ofloveandhate  
> Created at: 2018-08-17 21:46:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/63#issuecomment-413996369  

fixed, huzzah! 💯
