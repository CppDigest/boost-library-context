# #509 - Crash when initializing a cpp_bin_float_[...] with a specific value [Closed]

> Username: schwubdiwub  
> Created at: 2022-12-12 09:17:37 UTC  
> Updated at: 2023-02-09 11:59:43 UTC  
> Closed at: 2023-02-09 11:59:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/509  

When I run this code:  
```  
uint64_t doubleAsInt = 0xBFDFFFFFFFFFFFFC;  
const auto value = std::bit_cast<double>(doubleAsInt);  
boost::multiprecision::cpp_bin_float_quad v = value;  
```  
0xBFDFFFFFFFFFFFFC as double is -0.499999999999999777955395074...  
  
I get an error in [trunc.hpp](https://github.com/boostorg/multiprecision/blob/01ad9a0b12b9fb38ec34c4d98ea1b019d2bc1ccb/include/boost/multiprecision/detail/functions/trunc.hpp#L66).  
arg is 2147483647.999999, so 0.999999 bigger than INT_MAX.  
I tested this with cpp_bin_float_quad and cpp_bin_float_oct  
  
A fix that works for me is (in `itrunc` ):  
```  
if (arg >= static_cast<T>(INT_MAX) + static_cast<T>(1))  
{  
    BOOST_MP_THROW_EXCEPTION(std::domain_error("arg cannot be converted into an int"));  
}  
  
return static_cast<int>(boost::multiprecision::detail::impl::trunc(arg));  
```  
  
So comparing >= with INT_MAX + 1.  
As long as arg is smaller than INT_MAX, arg can be respresented correctly after trucation. Or is there something else I don't take into account? I will create a pull request for this.  
  
And if this is a valid fix, it possibly should be applied to `lltrunc`, too.
