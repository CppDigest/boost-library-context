# #46 - compile discrete_distribution for 64-bit code [Open]

> Username: Fuzzier  
> Created at: 2018-10-02 07:10:41 UTC  
> Updated at: 2018-10-28 16:11:44 UTC  
> Url: https://github.com/boostorg/random/issues/46  

When compiling discrete_distribution using MSVC 2010 64-bit compiler, warnings are emitted.  
The key warning says:  
warning C4267: 'argument' : conversion from 'size_t' to 'int32_t', possible loss of data  
  
Cause of the issue:  
In random/discrete_distribution.hpp, at line 507, the expression `_impl.get_weight(i)` tries to convert 'i' (a 'size_t') into 'IntType' (the parameter type of `get_weight()`).  
However, 'IntType' is 'int' by default, which causes a conversion from 64-bit to 32-bit in a 64-bit environment.  
  
Suggestions:  
At line 499, 'i' is defined as a 'size_t'.  
It should be safe to define it as an 'IntType':  
`IntType i = 0;`.

---

## Comment 1

> Username: drauch  
> Created at: 2018-10-28 16:11:44 UTC  
> Url: https://github.com/boostorg/random/issues/46#issuecomment-433718889  

MSVC 2010 is not supported by Boost, so it will probably not get fixed. See also https://www.boost.org/doc/libs/1_66_0/libs/convert/doc/html/boost_convert/supported_compilers.html for more information.  
  
And god help us, please update your compiler :-) The same is true for #45
