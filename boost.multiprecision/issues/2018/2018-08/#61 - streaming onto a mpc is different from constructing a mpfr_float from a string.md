# #61 - streaming onto a mpc is different from constructing a mpfr_float from a string [Closed]

> Username: ofloveandhate  
> Created at: 2018-08-09 20:54:54 UTC  
> Updated at: 2018-08-11 20:21:21 UTC  
> Closed at: 2018-08-11 20:21:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/61  

i have some unexpected results where streaming onto an existing complex yields different results from string construction.  
  
these 3 tests fail:  
```  
// fails on both real and imaginary  
BOOST_AUTO_TEST_CASE(complex_get_from_stream_parens_with_comma)  
{  
	std::stringstream ss;  
	ss << "(0.1234,-4.12397)";  
	  
	mpfr_complex z;  
	ss >> z;  
	  
	BOOST_CHECK_EQUAL(z.real(),mpfr_float("0.1234"));  
	BOOST_CHECK_EQUAL(z.imag(),mpfr_float("-4.12397"));  
}  
  
// fails on the real part  
BOOST_AUTO_TEST_CASE(complex_get_from_stream_parens_no_comma)  
{  
	std::stringstream ss;  
	ss << "(-3.651263418976498712e-2)";  
	  
	mpfr_complex z;  
	ss >> z;  
	  
	BOOST_CHECK_EQUAL(z.real(),mpfr_float("-3.651263418976498712e-2"));  
	BOOST_CHECK_EQUAL(z.imag(),mpfr_float("0"));  
}  
  
// fails on the real part  
BOOST_AUTO_TEST_CASE(complex_get_from_stream_no_parens)  
{  
	std::stringstream ss;  
	ss << "-3.651263418976498712e-2";  
	  
	mpfr_complex z;  
	ss >> z;  
	  
	BOOST_CHECK_EQUAL(z.real(),mpfr_float("-3.651263418976498712e-2"));  
	BOOST_CHECK_EQUAL(z.imag(),mpfr_float("0"));  
}  
```  
  
with output:  
  
```  
test/classes/complex_test.cpp:613: error: in "complex_multiprecision_class/complex_get_from_stream_parens_with_comma": check z.real() == mpfr_float("0.1234") has failed [0.1234 != 0.1234]  
test/classes/complex_test.cpp:614: error: in "complex_multiprecision_class/complex_get_from_stream_parens_with_comma": check z.imag() == mpfr_float("-4.12397") has failed [-4.12397 != -4.12397]  
test/classes/complex_test.cpp:626: error: in "complex_multiprecision_class/complex_get_from_stream_parens_no_comma": check z.real() == mpfr_float("-3.651263418976498712e-2") has failed [-0.0365126 != -0.0365126]  
test/classes/complex_test.cpp:639: error: in "complex_multiprecision_class/complex_get_from_stream_no_parens": check z.real() == mpfr_float("-3.651263418976498712e-2") has failed [-0.0365126 != -0.0365126]  
```  
  
however, doing the same stream operation with a real passes:  
  
```  
BOOST_AUTO_TEST_CASE(float_get_from_stream_parens_with_comma)  
{  
	std::stringstream ss;  
	ss << "0.1234";  
	  
	bertini::mpfr_float z;  
	ss >> z;  
	  
	BOOST_CHECK_EQUAL(z,bertini::mpfr_float("0.1234"));  
}  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-08-10 17:46:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/61#issuecomment-412155941  

There was a buglet fixed in the commit referenced above where some internal temporaries would be constructed to the default precision irrespective of the actual precision of the destination variable.  
  
However, apart from that, I'm unable to reproduce on either windows/msvc or ubuntu/g++.

---

## Comment 2

> Username: ofloveandhate  
> Created at: 2018-08-11 20:21:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/61#issuecomment-412299303  

i just pulled and re-compiled, and have not the problem at this moment.  i presume it is solved.  closing.
