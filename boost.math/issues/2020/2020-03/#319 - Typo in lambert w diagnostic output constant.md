# #319 - Typo in lambert w diagnostic output constant [Closed]

> Username: Gattag  
> Created at: 2020-03-02 18:38:25 UTC  
> Updated at: 2020-03-03 12:20:34 UTC  
> Closed at: 2020-03-03 12:20:34 UTC  
> Url: https://github.com/boostorg/math/issues/319  

There is a small typo in a constant used for diagnostic output in `special_functions/lambert_w.hpp`   
```C++  
#ifdef BOOST_MATH_INSTRUMENT_LAMBERT_W_TERMS  
  {  
    int terms = 20; // Default to using all terms.  
    if (absp < 0.001150)  
    { // Very near singularity.  
      terms = 6;  
    }  
```  
Should read `absp < 0.01159` instead
