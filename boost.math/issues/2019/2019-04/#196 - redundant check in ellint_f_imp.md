# #196 - redundant check in ellint_f_imp? [Closed]

> Username: shaneasd  
> Created at: 2019-04-14 11:40:05 UTC  
> Updated at: 2019-05-13 18:41:22 UTC  
> Closed at: 2019-05-13 18:41:11 UTC  
> Url: https://github.com/boostorg/math/issues/196  

on line 118ish there is a code snippet which looks like  
```  
       T sinp = sin(rphi);  
       sinp *= sinp;  
       T cosp = cos(rphi);  
       cosp *= cosp;  
       BOOST_MATH_INSTRUMENT_VARIABLE(sinp);  
       BOOST_MATH_INSTRUMENT_VARIABLE(cosp);  
       if(sinp > tools::min_value<T>())  
       {  
          //  
          // Use http://dlmf.nist.gov/19.25#E5, note that  
          // c-1 simplifies to cot^2(rphi) which avoid cancellation:  
          //  
          T c = 1 / sinp;  
          result = rphi == 0 ? static_cast<T>(0) : static_cast<T>(s * ellint_rf_imp(T(cosp / sinp), T(c - k * k), c, pol));  
       }  
       else  
          result = s * sin(rphi);  
```  
  
I can't see a scenario in which ```rphi==0``` could ever be true. If rphi==0 then sinp should also be zero for any canonical implementation of sin that I can think of. This means that if rphi==0 the ```sinp > tools::min_value<T>()``` check would have already failed (For any sensible implementation of min_value I can think of, keeping in mind the type can presumably store zero in order to be comparable to zero).  
  
It looks like the check on sinp was added in 61cc79bebdc8343bf3ad92d3681752a0c2c3385d and prior to this change the ```rphi==0``` check would presumably be required but insufficient. With this check I think it's now redundant.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-05-13 18:41:22 UTC  
> Url: https://github.com/boostorg/math/issues/196#issuecomment-491938906  

Good catch, thanks!
