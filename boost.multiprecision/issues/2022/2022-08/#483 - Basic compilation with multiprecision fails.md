# #483 - Basic compilation with multiprecision fails [Closed]

> Username: pederlh  
> Created at: 2022-08-12 13:33:24 UTC  
> Updated at: 2022-08-12 13:41:36 UTC  
> Closed at: 2022-08-12 13:41:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/483  

Hi,  
I am trying to implement variable-precision variables when using Boost/Math, but I am not able to include `<boost/multiprecision/mpfr.hpp>`. Compiling the tiny program  
```   
#include <boost/multiprecision/mpfr.hpp>  
int main() {return 0;}  
```  
gives an error  
```  
g++ -o minimal_example.exe minimal_example.cpp -I ../../../Boost/boost_1_79_0  
/usr/bin/ld: /tmp/cckggtFO.o: in function `boost::multiprecision::backends::detail::mpfr_cleanup<true>::initializer::~initializer()':  
minimal_example.cpp:(.text._ZN5boost14multiprecision8backends6detail12mpfr_cleanupILb1EE11initializerD2Ev[_ZN5boost14multiprecision8backends6detail12mpfr_cleanupILb1EE11initializerD5Ev]+0x11): undefined reference to `mpfr_free_cache'  
collect2: error: ld returned 1 exit status  
```  
and initializing a variable  
```  
#include <boost/multiprecision/mpfr.hpp>  
  
int main() {  
    boost::multiprecision::mpfr_float a = 1.5;  
    return 0;  
}  
```  
leads to even more errors (pasted below).  I run Ubuntu 20.04.04 on WSL and with gcc 9.4.0. Any ideas about what goes wrong?  
  
Best regards  
  
```  
/usr/bin/ld: /tmp/ccCny6Hb.o: in function `boost::multiprecision::backends::detail::mpfr_float_imp<0u, (boost::multiprecision::mpfr_allocation_type)1>::mpfr_float_imp()':  
minimal_example.cpp:(.text._ZN5boost14multiprecision8backends6detail14mpfr_float_impILj0ELNS0_20mpfr_allocation_typeE1EEC2Ev[_ZN5boost14multiprecision8backends6detail14mpfr_float_impILj0ELNS0_20mpfr_allocation_typeE1EEC5Ev]+0x2f): undefined reference to `mpfr_init2'  
/usr/bin/ld: minimal_example.cpp:(.text._ZN5boost14multiprecision8backends6detail14mpfr_float_impILj0ELNS0_20mpfr_allocation_typeE1EEC2Ev[_ZN5boost14multiprecision8backends6detail14mpfr_float_impILj0ELNS0_20mpfr_allocation_typeE1EEC5Ev]+0x45): undefined reference to `mpfr_set_ui'  
/usr/bin/ld: /tmp/ccCny6Hb.o: in function `boost::multiprecision::backends::detail::mpfr_float_imp<0u, (boost::multiprecision::mpfr_allocation_type)1>::~mpfr_float_imp()':  
minimal_example.cpp:(.text._ZN5boost14multiprecision8backends6detail14mpfr_float_impILj0ELNS0_20mpfr_allocation_typeE1EED2Ev[_ZN5boost14multiprecision8backends6detail14mpfr_float_impILj0ELNS0_20mpfr_allocation_typeE1EED5Ev]+0x25): undefined reference to `mpfr_clear'  
/usr/bin/ld: /tmp/ccCny6Hb.o: in function `std::enable_if<std::is_assignable<boost::multiprecision::backends::detail::mpfr_float_imp<0u, (boost::multiprecision::mpfr_allocation_type)1>, double>::value, boost::multiprecision::backends::mpfr_float_backend<0u, (boost::multiprecision::mpfr_allocation_type)1>&>::type boost::multiprecision::backends::mpfr_float_backend<0u, (boost::multiprecision::mpfr_allocation_type)1>::operator=<double>(double const&)':  
minimal_example.cpp:(.text._ZN5boost14multiprecision8backends18mpfr_float_backendILj0ELNS0_20mpfr_allocation_typeE1EEaSIdEENSt9enable_ifIXsrSt13is_assignableINS1_6detail14mpfr_float_impILj0ELS3_1EEET_E5valueERS4_E4typeERKSB_[_ZN5boost14multiprecision8backends18mpfr_float_backendILj0ELNS0_20mpfr_allocation_typeE1EEaSIdEENSt9enable_ifIXsrSt13is_assignableINS1_6detail14mpfr_float_impILj0ELS3_1EEET_E5valueERS4_E4typeERKSB_]+0x58): undefined reference to `mpfr_set_prec'  
/usr/bin/ld: /tmp/ccCny6Hb.o: in function `boost::multiprecision::backends::detail::mpfr_float_imp<0u, (boost::multiprecision::mpfr_allocation_type)1>::operator=(double)':  
minimal_example.cpp:(.text._ZN5boost14multiprecision8backends6detail14mpfr_float_impILj0ELNS0_20mpfr_allocation_typeE1EEaSEd[_ZN5boost14multiprecision8backends6detail14mpfr_float_impILj0ELNS0_20mpfr_allocation_typeE1EEaSEd]+0x41): undefined reference to `mpfr_init2'  
/usr/bin/ld: minimal_example.cpp:(.text._ZN5boost14multiprecision8backends6detail14mpfr_float_impILj0ELNS0_20mpfr_allocation_typeE1EEaSEd[_ZN5boost14multiprecision8backends6detail14mpfr_float_impILj0ELNS0_20mpfr_allocation_typeE1EEaSEd]+0x5b): undefined reference to `mpfr_set_d'  
/usr/bin/ld: /tmp/ccCny6Hb.o: in function `boost::multiprecision::backends::detail::mpfr_cleanup<true>::thread_initializer::~thread_initializer()':  
minimal_example.cpp:(.text._ZN5boost14multiprecision8backends6detail12mpfr_cleanupILb1EE18thread_initializerD2Ev[_ZN5boost14multiprecision8backends6detail12mpfr_cleanupILb1EE18thread_initializerD5Ev]+0x16): undefined reference to `mpfr_free_cache2'  
/usr/bin/ld: /tmp/ccCny6Hb.o: in function `boost::multiprecision::backends::detail::mpfr_cleanup<true>::initializer::~initializer()':  
minimal_example.cpp:(.text._ZN5boost14multiprecision8backends6detail12mpfr_cleanupILb1EE11initializerD2Ev[_ZN5boost14multiprecision8backends6detail12mpfr_cleanupILb1EE11initializerD5Ev]+0x11): undefined reference to `mpfr_free_cache'  
collect2: error: ld returned 1 exit status  
```

---

## Comment 1

> Username: pederlh  
> Created at: 2022-08-12 13:41:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/483#issuecomment-1213124454  

I didn't realize from reading [the docs](https://www.boost.org/doc/libs/1_69_0/libs/multiprecision/doc/html/boost_multiprecision/tut/floats/mpfr_float.html) that I have to link `-lmpfr` when compiling the code, everything works after the flag is added...
