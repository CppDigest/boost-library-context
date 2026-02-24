# #586 - Can't convert cpp_dec_float to cpp_int [Closed]

> Username: radj307  
> Created at: 2024-01-21 22:25:11 UTC  
> Updated at: 2024-01-23 17:06:10 UTC  
> Closed at: 2024-01-23 17:05:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/586  

I'm using `cpp_int` and `cpp_dec_float_100` in a project, but I'm encountering an error when trying to convert from float to int on both MSVC 19.34.31947 and gcc 10, using boostmp 1.84 standalone.  
  
```cpp  
#include <boost/multiprecision/cpp_int.hpp>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
namespace mp = boost::multiprecision;  
  
int main()  
{  
	using mp::cpp_int;  
	using cpp_float = mp::cpp_dec_float_100;  
  
	cpp_float r1{ static_cast<cpp_float>(cpp_int{ 5 }) }; //< works  
	cpp_int r2{ static_cast<cpp_int>(cpp_float{ 5.5 }) }; //< ERROR: (see below)  
  
	return 0;  
}  
```  
  
This is the error message in GCC 10 (formatted for readability):  
  
```  
operands to ‘?:’ have different types  
‘std::enable_if<true, boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::detail::number_kind_floating_pointfloor_funct<boost::multiprecision::backends::cpp_dec_float<100> >, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100> >, void, void> >::type’  
  {aka ‘boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::detail::number_kind_floating_pointfloor_funct<boost::multiprecision::backends::cpp_dec_float<100> >, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100> >, void, void>’}  
and  
‘std::enable_if<true, boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::detail::number_kind_floating_pointceil_funct<boost::multiprecision::backends::cpp_dec_float<100> >, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100> >, void, void> >::type’  
  {aka ‘boost::multiprecision::detail::expression<boost::multiprecision::detail::function, boost::multiprecision::detail::number_kind_floating_pointceil_funct<boost::multiprecision::backends::cpp_dec_float<100> >, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100> >, void, void>’}  
(...)\boost\multiprecision\detail\functions\trunc.hpp  
```  
  
The error does not occur when using Boost::math alongside it.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2024-01-22 06:14:30 UTC  
> Updated at: 2024-01-22 06:22:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/586#issuecomment-1903330937  

>  I'm encountering an error when trying to convert   
  
Hi @radj307 great catch and thanks for the report.  
  
I can reproduce this situation exactly with modular-boost on today's develop-branch as well.  
  
The phenomenon seems to arise from [detail/functions/trunc.hpp line 29](https://github.com/boostorg/multiprecision/blob/878138ad145254c95ada46203505036c1d8d55f4/include/boost/multiprecision/detail/functions/trunc.hpp#L29).  
  
The `operator ?` mentioned is the ternary operator at that line where the arguments (i.e., the compiler's decuced result types from `floor`and `ceil`) differ. One is the backend type alone whereby the other is the fully number-wrapped backend type.  
  
I was not, sadly, alone able to find the root cause.  
  
But if it helps investigations, rewriting [`impl::trunc()`](https://github.com/boostorg/multiprecision/blob/878138ad145254c95ada46203505036c1d8d55f4/include/boost/multiprecision/detail/functions/trunc.hpp#L24) in the following way eliminates the problem. But this is not a great solution suggestoin. I'd like to see why the overloads have differences. But alone I couldn't figure it out (yet).  
  
Another detail, this `trunc`is called from `lltrunc` in the same header around line 59. So that's probably closer to the place where the arguments for the overloads are determined, probably even higher upstream in the call chain actually.  
  
```  
template <typename T>  
inline T trunc BOOST_PREVENT_MACRO_SUBSTITUTION (const T arg)  
{  
    using std::floor;  
    using std::ceil;  
  
    T t { };  
  
    if (arg > T(0))  
    {  
        t = floor(arg);  
    }  
    else  
    {  
        t = ceil(arg);  
    }  
  
    return t;  
}  
```  
  
Cc: @mborland and @jzmaddock

---

## Comment 2

> Username: ckormanyos  
> Created at: 2024-01-22 18:18:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/586#issuecomment-1904555738  

See also #587, which might not be the last word on this, but offers a preliminary (perhaps legitimate, long-lasting) fix.  
  
Cc: @jzmaddock and @mborland

---

## Comment 3

> Username: ckormanyos  
> Created at: 2024-01-23 17:06:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/586#issuecomment-1906526684  

Fixed in #587
