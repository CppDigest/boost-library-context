# #29 - Ill-formed narrowing conversions for ppc [Open]

> Username: jwakely  
> Created at: 2022-01-31 11:33:51 UTC  
> Updated at: 2022-01-31 11:33:51 UTC  
> Url: https://github.com/boostorg/interval/issues/29  

```  
/usr/include/boost/numeric/interval/detail/ppc_rounding_control.hpp:31:56: error: narrowing conversion of '18444492273895866370' from 'long long unsigned int' to 'boost::long_long_type' {aka 'long long int'} [-Wnarrowing]  
   31 | static const rounding_mode_struct mode_upward      = { 0xFFF8000000000002LL };  
      |                                                        ^~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/numeric/interval/detail/ppc_rounding_control.hpp:32:56: error: narrowing conversion of '18444492273895866371' from 'long long unsigned int' to 'boost::long_long_type' {aka 'long long int'} [-Wnarrowing]  
   32 | static const rounding_mode_struct mode_downward    = { 0xFFF8000000000003LL };  
      |                                                        ^~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/numeric/interval/detail/ppc_rounding_control.hpp:33:56: error: narrowing conversion of '18444492273895866368' from 'long long unsigned int' to 'boost::long_long_type' {aka 'long long int'} [-Wnarrowing]  
   33 | static const rounding_mode_struct mode_to_nearest  = { 0xFFF8000000000000LL };  
      |                                                        ^~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/numeric/interval/detail/ppc_rounding_control.hpp:34:56: error: narrowing conversion of '18444492273895866369' from 'long long unsigned int' to 'boost::long_long_type' {aka 'long long int'} [-Wnarrowing]  
   34 | static const rounding_mode_struct mode_toward_zero = { 0xFFF8000000000001LL };  
      |                                                        ^~~~~~~~~~~~~~~~~~~~  
```  
  
These constants are too large for `long long` so they have type `unsigned long long` (despite the LL suffix). Converting them to `long long` inside a braced-init-list is a narrowing conversion.
