# #252 - Return value ignored: 'frexp' - Warning C6031 [Closed]

> Username: Riadela  
> Created at: 2019-09-12 09:45:05 UTC  
> Updated at: 2019-09-13 08:10:42 UTC  
> Closed at: 2019-09-13 08:10:41 UTC  
> Url: https://github.com/boostorg/math/issues/252  

Hi,  
I'm trying to build my project in release mode, but I keep getting this warning. which is treated as an error, from math\special_functions\next.hpp row 493 and row 503  
I'm using Windows SDK 10, VS 17 (msvc 141) and Boost 1.71  
In the SDK file  
> c:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.14.26428\include\cmath  
  
The two functions that give me the warning are defined in this way  
```  
_Check_return_ inline float frexp(_In_ float _Xx, _Out_ int* _Yx) _NOEXCEPT  
	{  
	return (_CSTD frexpf(_Xx, _Yx));  
	}  
```  
And   
```  
_Check_return_ inline long double frexp(_In_ long double _Xx,  
	_Out_ int* _Yx) _NOEXCEPT  
	{  
	return (_CSTD frexpl(_Xx, _Yx));  
	}  
```  
  
Is there anyway of fixing this issue, or should I just disable the warning?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-09-12 11:19:24 UTC  
> Url: https://github.com/boostorg/math/issues/252#issuecomment-530780335  

Strangely I'm not able to reproduce, even though I do see the same definitions for frexp as you do.  Since we really don't need to the function result (just the exponent), does casting the call to void fix things, as in :  
  
```  
(void)frexp(((boost::math::fpclassify)(a) == (int)FP_SUBNORMAL) ? tools::min_value<T>() : a, &expon);  
```  
  
?

---

## Comment 2

> Username: Riadela  
> Created at: 2019-09-13 08:10:41 UTC  
> Url: https://github.com/boostorg/math/issues/252#issuecomment-531143532  

Yes it does.  
Thank you anyway for your help!
