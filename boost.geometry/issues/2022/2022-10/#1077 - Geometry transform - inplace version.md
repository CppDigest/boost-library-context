# #1077 - Geometry transform : inplace version? [Closed]

> Username: ghost  
> Created at: 2022-10-22 07:08:16 UTC  
> Updated at: 2022-11-11 11:57:17 UTC  
> Closed at: 2022-11-11 11:57:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/1077  

Hi,  
Could the boost::geometry::strategy::transform::rotate/translate/scale be made in place?  
  
It seems that having a different output geometry is a waste. Since we don't modify the number of points, it should be doable.  
  
What is your opinion?  
Thanks
