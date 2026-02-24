# #112 - static_cast generates warning [Closed]

> Username: PeterSommerlad  
> Created at: 2021-09-08 12:53:32 UTC  
> Updated at: 2021-09-08 12:55:32 UTC  
> Closed at: 2021-09-08 12:54:43 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/112  

```  
/usr/local/include/boost/safe_numerics/checked_result_operations.hpp: In function 'std::basic_ostream<_CharT, _Traits>& std::operator<<(std::basic_ostream<_CharT, _Traits>&, const boost::safe_numerics::checked_result<signed char>&)':  
/usr/local/include/boost/safe_numerics/checked_result_operations.hpp:1149:58: warning: type qualifiers ignored on cast result type [-Wignored-qualifiers]  
 1149 |         os << std::error_code(r.m_e).message() << ':' << static_cast<const char * const>(r);  
      |                                                          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/safe_numerics/checked_result_operations.hpp: In function 'std::basic_istream<_CharT, _Traits>& std::operator>>(std::basic_istream<_CharT, _Traits>&, boost::safe_numerics::checked_result<signed char>&)':  
/usr/local/include/boost/safe_numerics/checked_result_operations.hpp:1180:58: warning: type qualifiers ignored on cast result type [-Wignored-qualifiers]  
 1180 |         is >> std::error_code(r.m_e).message() >> ':' >> static_cast<const char * const>(r);  
      |                                                          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
I believe the right most const is generating the warning, because it is irrelevant for any rvalue.  
  
I would judge this as a call out for "East const" style, but others might not agree....

---

## Comment 1

> Username: PeterSommerlad  
> Created at: 2021-09-08 12:54:43 UTC  
> Updated at: 2021-09-08 12:55:32 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/112#issuecomment-915213314  

I see, already fixed. just not yet available downstream.
