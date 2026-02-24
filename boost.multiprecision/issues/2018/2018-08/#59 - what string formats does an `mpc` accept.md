# #59 - what string formats does an `mpc` accept? [Closed]

> Username: ofloveandhate  
> Created at: 2018-08-05 04:53:41 UTC  
> Updated at: 2018-08-07 14:40:35 UTC  
> Closed at: 2018-08-05 19:39:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/59  

when constructing an `mpc`, what formats will the string constructor accept?  i see in the documentation for 1.68.0_beta1, that  
  
    Conversion from a string results in a std::runtime_error being thrown if the string can not be interpreted as a valid complex number  
  
what are the valid string formats?

---

## Comment 1

> Username: ofloveandhate  
> Created at: 2018-08-05 05:09:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/59#issuecomment-410496324  

here are the specific errors i am getting:  
  
```  
OMITTED/include/boost/multiprecision/mpfr.hpp:206: fatal error: in "mpfr_float_imp<digits10, boost::multiprecision::mpfr_allocation_type::allocate_dynamic> &boost::multiprecision::backends::detail::mpfr_float_imp<0, boost::multiprecision::mpfr_allocation_type::allocate_dynamic>::operator=(const char *) [digits10 = 0, AllocationType = boost::multiprecision::mpfr_allocation_type::allocate_dynamic]": boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<std::runtime_error> >:   
Unable to parse string ""as a valid floating point number.  
  
  
OMITTED/include/boost/multiprecision/mpc.hpp:211: fatal error: in "mpc_complex_imp<digits10> &boost::multiprecision::backends::detail::mpc_complex_imp<0>::operator=(const char *) [digits10 = 0]": boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<std::runtime_error> >:   
Unable to parse string "-3.651263418976498712e-2"as a valid floating point number.  
  
  
OMITTED/include/boost/multiprecision/mpc.hpp:211: fatal error: in "mpc_complex_imp<digits10> &boost::multiprecision::backends::detail::mpc_complex_imp<0>::operator=(const char *) [digits10 = 0]": boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<std::runtime_error> >:   
Unable to parse string "1.23124"as a valid floating point number.  
```  
  
with these strings:  
  
* `1.23124`   
* `(-3.651263418976498712e-2)`   
* `-3.651263418976498712e-2`   
  
and the form of one of these tests looking like:  
  
```  
BOOST_AUTO_TEST_CASE(complex_get_from_stream_parens_with_comma)  
{  
	std::stringstream ss;  
	ss << "1.23124";  
	  
	bertini::mpfr_complex z;  
	ss >> z;  
	  
	BOOST_CHECK_EQUAL(z.real(),bertini::mpfr_float("0.1234"));  
	BOOST_CHECK_EQUAL(z.imag(),bertini::mpfr_float("-4.12397"));  
}  
  
```  
  
where `bertini::mpfr_complex` is just a variable-precision `mpc`:   
  
```  
using mpfr_complex = bmp::number<mpc_complex_backend<0>, bmp::et_on >;  
```  
  
that is, `mpc`'s currently can't be constructed from a "real" string, but it would be very convenient to, i think.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-08-05 07:38:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/59#issuecomment-410502071  

That's strange, works for me with cpp_complex.  I don't have mpfr setup yet (new machine!) will report back when I do.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2018-08-05 08:13:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/59#issuecomment-410503820  

OK, it's because MPC doesn't accept single-valued strings to mpc_set_string :(  
  
Will try to find a workaround.

---

## Comment 4

> Username: ofloveandhate  
> Created at: 2018-08-07 14:40:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/59#issuecomment-411080568  

is fixed, thanks!
