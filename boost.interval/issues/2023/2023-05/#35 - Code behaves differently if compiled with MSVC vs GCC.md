# #35 - Code behaves differently if compiled with MSVC vs GCC [Open]

> Username: olologin  
> Created at: 2023-05-17 17:48:56 UTC  
> Updated at: 2023-05-17 17:50:32 UTC  
> Url: https://github.com/boostorg/interval/issues/35  

VS 19 vs GCC 12  
If you build and run the following sample with both compilers:  
  
```  
#include <fenv.h>  
#include <iostream>  
#pragma warning(push)  
#pragma warning(disable : 4702)  
#include <boost/numeric/interval.hpp>  
#pragma warning(pop)  
  
/// An interval of doubles [lower,upper)  
using Interval = boost::numeric::interval<double,boost::numeric::interval_lib::policies<boost::numeric::interval_lib::rounded_math<double>,boost::numeric::interval_lib::checking_strict<double>>>;  
  
/// An interval from -DBL_MAX to +DBL_MAX  
const inline Interval InfiniteInterval =  
	Interval(std::numeric_limits<double>::lowest(), std::numeric_limits<double>::max());  
  
int main(void)  
{  
#ifdef _WIN32  
	unsigned int current_word = 0;  
	_controlfp_s(&current_word, 0, _MCW_EM);  
#else  
	feenableexcept(FE_ALL_EXCEPT);  
#endif  
	auto i = InfiniteInterval;  
	boost::numeric::width(i);  
	std::cerr << "The end" << std::endl;  
}  
```  
You will see how it fails with FP exception on linux, but never fails on windows.  
The reason is simple:  
On linux we adjust only rounding mode https://github.com/boostorg/interval/blob/2eda7413ac16dd4158005446438daf8a7e435dd9/include/boost/numeric/interval/detail/c99sub_rounding_control.hpp#L27  
But on windows in addition to rounding we also mask all the FP exceptions (turn off corresponding flags) https://github.com/boostorg/interval/blob/2eda7413ac16dd4158005446438daf8a7e435dd9/include/boost/numeric/interval/detail/msvc_rounding_control.hpp#L93 because we use `_MCW_EM | _MCW_RC` instead of _MCW_RC only.  
  
Shouldn't both systems set the same FP settings? If yes - what should they adjust? Both systems should adjust rounding only, or both should adjust rounding and exception masking?  
If we only want to change rounding control bits - it is enough to remove `_MCW_EM |` part of that line and both systems will throw FP exception.

---

## Comment 1

> Username: olologin  
> Created at: 2023-05-17 17:50:32 UTC  
> Url: https://github.com/boostorg/interval/issues/35#issuecomment-1551822927  

Maybe I can help with PR if you decide on what should be the preferred behavior.
