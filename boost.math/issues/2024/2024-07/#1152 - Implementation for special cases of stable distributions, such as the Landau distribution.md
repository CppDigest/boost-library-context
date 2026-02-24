# #1152 - Implementation for special cases of stable distributions, such as the Landau distribution [Closed]

> Username: tk-yoshimura  
> Created at: 2024-07-11 02:09:05 UTC  
> Updated at: 2024-07-29 19:55:19 UTC  
> Closed at: 2024-07-29 19:55:18 UTC  
> Url: https://github.com/boostorg/math/issues/1152  

I would like to implement a special case of stable distribution in boost.  
  
For the following distributions, coefficients that can approximate float64 and float128 with machine epsilon accuracy have been obtained.  
- Landau Distribution (*&alpha;*=1, *&beta;*=1)    
  [C++, Csharp](https://github.com/tk-yoshimura/LandauDistributionFP64)    
  [ResearchGate](https://www.researchgate.net/publication/382141401_Numerical_Evaluation_and_High_Precision_Approximation_Formula_for_Landau_Distribution)    
  [DOI](https://doi.org/10.36227/techrxiv.171822215.53612870/v2)  
- Map-Airy Distribution (*&alpha;*=3/2, *&beta;*=1)    
  [C++, Csharp](https://github.com/tk-yoshimura/MapAiryDistributionFP64)    
  [ResearchGate](https://www.researchgate.net/publication/382138858_Numerical_Evaluation_and_High_Precision_Approximation_Formula_for_Map-Airy_Distribution)  
  [DOI](http://dx.doi.org/10.36227/techrxiv.172053942.27675733/v1)  
- Holtsmark Distribution (*&alpha;*=3/2, *&beta;*=0)    
  [C++, Csharp](https://github.com/tk-yoshimura/HoltsmarkDistributionFP64)    
  [ResearchGate](https://www.researchgate.net/publication/382127111_Numerical_Evaluation_and_High_Precision_Approximation_Formula_for_Holtsmark_Distribution)  
  [DOI](http://dx.doi.org/10.36227/techrxiv.172054657.73020014/v1)  
- S&alpha;S point5 Distribution (*&alpha;*=1/2, *&beta;*=0)    
  [C++, Csharp](https://github.com/tk-yoshimura/SaSPoint5DistributionFP64)    
  [ResearchGate](https://www.researchgate.net/publication/382124528_Numerical_Evaluation_and_High_Precision_Approximation_Formula_for_SaS_point5_Distribution)  
  [DOI](http://dx.doi.org/10.36227/techrxiv.172055253.37208198/v1)  
  
I know these distributions are used infrequently, but if you need them for boost, please let me know and I will help you implement them!

---

## Comment 1

> Username: mborland  
> Created at: 2024-07-11 13:02:05 UTC  
> Url: https://github.com/boostorg/math/issues/1152#issuecomment-2222884321  

If you have implementations that you would like to donate I see no reason to turn them down. Let me know what questions you have. You'll find implementations of all of our current distributions in `include/boost/math/distributions` and a whole host of example tests in the `tests` folder.

---

## Comment 2

> Username: tk-yoshimura  
> Created at: 2024-07-12 19:16:07 UTC  
> Url: https://github.com/boostorg/math/issues/1152#issuecomment-2226215917  

@mborland   
Thank you very much.  
To approximate these distributions it is necessary to use the Padé approximation. Would it be correct to refer to the implementation of the zeta function?  
https://github.com/boostorg/math/blob/develop/include/boost/math/special_functions/zeta.hpp  
  
boost uses so many templates and macros that I am going to have a hard time getting used to reading it.  
  
Is it correct to understand that RealType and T used in the distribution implementation refer to the same class?  
```cpp  
template <class T, class Policy>  
template <class RealType, class Policy>  
```

---

## Comment 3

> Username: mborland  
> Created at: 2024-07-12 19:45:32 UTC  
> Url: https://github.com/boostorg/math/issues/1152#issuecomment-2226250889  

> @mborland   
>   
> Thank you very much.  
>   
> To approximate these distributions it is necessary to use the Padé approximation. Would it be correct to refer to the implementation of the zeta function?  
>   
> https://github.com/boostorg/math/blob/develop/include/boost/math/special_functions/zeta.hpp  
>   
>   
>   
> boost uses so many templates and macros that I am going to have a hard time getting used to reading it.  
>   
>   
>   
> Is it correct to understand that RealType and T used in the distribution implementation refer to the same class?  
>   
> ```cpp  
>   
> template <class T, class Policy>  
>   
> template <class RealType, class Policy>  
>   
> ```  
  
It looks like the zeta function would be a good starting point. T and RealType would be the same. Prefer using RealType as it's more descriptive to someone reading your code than T. @ckormanyos can also likely assist with using Padé approximations as he has worked a number of those for other functions in recent months.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2024-07-13 07:03:10 UTC  
> Url: https://github.com/boostorg/math/issues/1152#issuecomment-2226801452  

> can also likely assist with using Padé approximations as he has worked a number of those for other functions in recent months  
  
I hope so, if it helps. We did, indeed, work out quite a few Pade approximants for 32, 64 and 128 bit calculations in recent months. Feel free to get back to me if any are needed.

---

## Comment 5

> Username: mborland  
> Created at: 2024-07-29 19:55:18 UTC  
> Url: https://github.com/boostorg/math/issues/1152#issuecomment-2256782611  

I believe this is now complete. See: #1159, #1163 for the implementations, and #1165 for addition of GPU support.
