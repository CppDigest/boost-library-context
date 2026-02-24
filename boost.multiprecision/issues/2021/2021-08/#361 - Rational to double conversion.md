# #361 - Rational to double conversion [Closed]

> Username: leoneifler  
> Created at: 2021-08-26 12:46:27 UTC  
> Updated at: 2021-12-04 13:53:40 UTC  
> Closed at: 2021-12-04 13:53:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/361  

While comparing the use of multiprecision rationals using the gmp backend and directly using gmp, I noticed that the multiprecision library does not use `mpq_get_d` to convert to double (in `gmp.hpp`), rather it uses `generic_convert_rational_to_float` with the comment that `mpq_get_d` "does not round correctly".  
  
Does anyone have more insight into how this incorrect rounding looks like? It seems that using the built-in `mpq_get_d` is quite a bit faster.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-08-26 17:52:47 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/361#issuecomment-906618417  

You would need to uncomment the old code and run the tests to be sure, but from memory I believe I was seeing no particular rounding.

---

## Comment 2

> Username: leoneifler  
> Created at: 2021-08-26 19:06:19 UTC  
> Updated at: 2021-08-26 19:06:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/361#issuecomment-906668799  

Thanks for replying. I uncommented and ran the old code, and it seemed to work fine with me (also never really had issues with using `mpq_get_d` in the past). Do you mean by "seeing no particular rounding" that the rounding direction was not consistent? (I never checked that)

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-08-26 19:22:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/361#issuecomment-906679374  

Here's a failed test case, rounding:  
  
84884208345301765479277106829122791378244511140079838970257821763490359020857494596489800743887621874805656550709985221024548147582870403432687120225160646050840770911714493962130984621866635780851346898663641833443001971842920632702468096  
  
To double produces 8.488420834530175680e+238   
rather than              8.488420834530176837e+238  
  
The test program that fails for me is test_rat_float_interconv.cpp built with -DTEST5.  
  
Tested with mingw msys.

---

## Comment 4

> Username: mglisse  
> Created at: 2021-08-27 19:42:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/361#issuecomment-907434945  

`mpq_get_d` is documented to round towards 0, while IIRC boost wants to round to nearest.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2021-12-04 13:53:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/361#issuecomment-986030752  

Thanks @mglisse , closing for now, please reopen if there's a better solution.
