# #47  Introduce support for Boost.MP11 [Merged]

> Username: CromwellEnage  
> Created at: 2019-01-04 07:34:59 UTC  
> Updated at: 2019-01-15 03:54:08 UTC  
> Merged at: 2019-01-14 17:43:31 UTC  
> Closed at: 2019-01-14 17:43:31 UTC  
> Url: https://github.com/boostorg/parameter/pull/47  

<boost/parameter/config.hpp>  
* Introduce configuration macros BOOST_PARAMETER_CAN_USE_MP11 and BOOST_PARAMETER_DISABLE_MP11_USAGE.  
* Update perfect forwarding support requirements to fix regressions on test matrix.  
  
<boost/parameter/parameters.hpp>  
* Allow mp11::mp_list to be default parameter_spec type if available.

---
