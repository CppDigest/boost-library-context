# #69 - unable to pow(complex,expression(real)) [Closed]

> Username: ofloveandhate  
> Created at: 2018-08-16 16:09:48 UTC  
> Updated at: 2018-08-18 08:39:27 UTC  
> Closed at: 2018-08-18 08:38:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/69  

there's no overload for taking complex^expression(real).  this test fails to compile:  
  
```  
BOOST_AUTO_TEST_CASE(complex_pow_expressionofreal)  
{  
	DefaultPrecision(50);  
	  
	mpfr_complex z("1.5", "2.25");  
	mpfr_float v("-3.1");  
	mpfr_complex w = pow(z,2*v);  
		  
	BOOST_CHECK(abs(real(w)- mpfr_float("0.0020583559161721337087583598028377697763165142184117220013738")) < threshold_clearance_mp);  
	// this value computed with matlab's vpa.  
	BOOST_CHECK(abs(imag(w)- mpfr_float("-0.000395572868694696378274143631981503385520868787960481823572936")) < threshold_clearance_mp);  
	// this value computed with matlab's vpa.  
}  
```  
where DefaultPrecision adjusts both the real and complex precisions.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-08-18 08:39:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/69#issuecomment-414042139  

That one should be fixed now.  
  
Note that pow(expression, expression) is not supported unless both expressions are of the same type - that one gets complicated :(
