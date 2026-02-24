# #33 - undefined behavior in gcd when parameters are minimum values of signed integers [Open]

> Username: jmcclellen  
> Created at: 2024-04-18 19:42:52 UTC  
> Updated at: 2024-04-19 16:04:33 UTC  
> Url: https://github.com/boostorg/integer/issues/33  

This is being reported to me via a clang-analyzer check, and it refers to the gcd template function in common_factor_rt.hpp  
  
Integer template parameter type is a signed integer, and both parameters ('a' and 'b') are  std::numeric_limits\<Integer\>::min.  
  
```  
444 template <typename Integer>  
445 inline BOOST_CXX14_CONSTEXPR Integer gcd(Integer const &a, Integer const &b) BOOST_GCD_NOEXCEPT(Integer)  
446 {  
447     if(a == (std::numeric_limits<Integer>::min)())  
448        return a == static_cast<Integer>(0) ? gcd_detail::gcd_traits<Integer>::abs(b) : boost::integer::gcd(static_cast<Integer>(a % b), b);  
449     else if (b == (std::numeric_limits<Integer>::min)())  
450        return b == static_cast<Integer>(0) ? gcd_detail::gcd_traits<Integer>::abs(a) : boost::integer::gcd(a, static_cast<Integer>(b % a));  
451     return gcd_detail::optimal_gcd_select(static_cast<Integer>(gcd_detail::gcd_traits<Integer>::abs(a)), static_cast<Integer>(gcd_detail::gcd_traits<Integer>::abs(b)));  
452 }  
```  
In the initial invocation, the condition on 447 is true, and the condition on 448 is false. This leads to a recursive call to the function with parameters a=0 and b=std::numeric_limits\<Integer\>::min.  
  
In the recursive call, the condition on 447 is false, the condition on 449 is true, and the condition on 450 is false. Another recursive call is on-deck with a=0 and b=b%a, But a is zero at this point. Anything mod 0 is undefined behavior.  
  
My suggested fix is to check if a and b are the same value at the start of the function, and return a right away.

---

## Comment 1

> Username: jmcclellen  
> Created at: 2024-04-19 16:04:33 UTC  
> Url: https://github.com/boostorg/integer/issues/33#issuecomment-2066871934  

I suppose I should have mentioned that I am using version 1.80, but I did confirm that the same code exists in 1.85 and in a git checkout.  
  
This was exposed by enabling clang-analyzer-* checks in clang-tidy (version 14.0.0) on a linux server. Compiler is g++ version 11.1.0.  
  
Here is the important part of clang-tidy's output:  
  
```  
boost/integer/common_factor_rt.hpp:447:8: note: Assuming the condition is true  
    if(a == (std::numeric_limits<Integer>::min)())  
       ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/integer/common_factor_rt.hpp:447:5: note: Taking true branch  
    if(a == (std::numeric_limits<Integer>::min)())  
    ^  
boost/integer/common_factor_rt.hpp:448:15: note: 'a' is not equal to 0  
       return a == static_cast<Integer>(0) ? gcd_detail::gcd_traits<Integer>::abs(b) : boost::integer::gcd(static_cast<Integer>(a % b), b);  
              ^  
boost/integer/common_factor_rt.hpp:448:15: note: '?' condition is false  
boost/integer/common_factor_rt.hpp:448:88: note: Calling 'gcd<long long>'  
       return a == static_cast<Integer>(0) ? gcd_detail::gcd_traits<Integer>::abs(b) : boost::integer::gcd(static_cast<Integer>(a % b), b);  
                                                                                       ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/integer/common_factor_rt.hpp:447:5: note: Taking false branch  
    if(a == (std::numeric_limits<Integer>::min)())  
    ^  
boost/integer/common_factor_rt.hpp:449:14: note: Assuming the condition is true  
    else if (b == (std::numeric_limits<Integer>::min)())  
             ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
boost/integer/common_factor_rt.hpp:449:10: note: Taking true branch  
    else if (b == (std::numeric_limits<Integer>::min)())  
         ^  
boost/integer/common_factor_rt.hpp:450:15: note: 'b' is not equal to 0  
       return b == static_cast<Integer>(0) ? gcd_detail::gcd_traits<Integer>::abs(a) : boost::integer::gcd(a, static_cast<Integer>(b % a));  
              ^  
boost/integer/common_factor_rt.hpp:450:15: note: '?' condition is false  
boost/integer/common_factor_rt.hpp:450:134: note: The result of the '' expression is undefined  
       return b == static_cast<Integer>(0) ? gcd_detail::gcd_traits<Integer>::abs(a) : boost::integer::gcd(a, static_cast<Integer>(b % a));  
                                                                                                                                   ~~^~~  
```
