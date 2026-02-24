# #112 - Renaming shape() to extend(), dim() to rank() and uoflow() to options() [Closed]

> Username: HDembinski  
> Created at: 2018-10-01 10:25:26 UTC  
> Updated at: 2018-10-27 17:48:15 UTC  
> Closed at: 2018-10-27 17:48:15 UTC  
> Url: https://github.com/boostorg/histogram/issues/112  

Bjørn Reese:  
  
> Related to this, I also like the extent() over shape(). This name is  
also used by Boost.MultiArray and the std::mdspan proposal (P0009R7).  
In the name of consistency, you may also consider renaming dim() to  
rank().

---

## Comment 1

> Username: jpivarski  
> Created at: 2018-10-01 10:58:22 UTC  
> Url: https://github.com/boostorg/histogram/issues/112#issuecomment-425867548  

Even though I want to use this with Numpy, I agree that consistency with Boost is better than consistency with Numpy.
