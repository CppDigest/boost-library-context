# #65 - increasing precision of mpc produces nan [Closed]

> Username: ofloveandhate  
> Created at: 2018-08-13 22:24:12 UTC  
> Updated at: 2018-08-16 07:52:19 UTC  
> Closed at: 2018-08-16 07:52:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/65  

making a complex number, then increasing its precision, produces `(nan,nan)`.  test below.  
  
```  
// doesn't fail  
BOOST_AUTO_TEST_CASE(real_precision_increase)  
{  
	DefaultPrecision(50);  
	mpfr_float a(2);  
	a.precision(100);  
	std::cout << a << '\n';  // just 2  
	BOOST_CHECK(abs(mpfr_float(2)-a) < 1e-50);  
}  
  
//fails  
BOOST_AUTO_TEST_CASE(complex_precision_increase)  
{  
	DefaultPrecision(50);  
	mpfr_complex a(2,3);  
	a.precision(100);  
	std::cout << a << '\n';  // (nan,nan) for me...  
	BOOST_CHECK(abs(mpfr_complex(2,3)-a) < 1e-50);  
}  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-08-15 18:32:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/65#issuecomment-413292335  

Confirmed, should be fixed in develop.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-08-16 07:52:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/65#issuecomment-413458089  

CI passing, closing.
