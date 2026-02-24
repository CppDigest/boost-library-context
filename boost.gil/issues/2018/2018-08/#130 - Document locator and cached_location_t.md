# #130 - Document locator and cached_location_t [Closed]

> Username: mloskot  
> Created at: 2018-08-25 16:00:46 UTC  
> Updated at: 2019-04-15 22:31:46 UTC  
> Closed at: 2019-04-15 22:31:46 UTC  
> Url: https://github.com/boostorg/gil/issues/130  

(moved from https://svn.boost.org/trac10/ticket/8144)  
  
I've stumbled upon old thread [on ​locator and cached_location_t](https://lists.boost.org/Archives/boost/2010/12/174157.php). There are some interesting explanations which I believe is worth to add to Boost.GIL docs.  
  
Particularly, I believe the following Lubomir's clarification should be added as first paragraph documenting cached location:  
  
> Cached location is meant to be used as a quick way to get to neighborhood pixels  
> if you need to read or write a neighboring location frequently relative to a given locator.  
  
Currently, notion of cached location is documented in the [GIL design guide](https://www.boost.org/libs/gil/doc/html/design_guide.html) but it makes users to deduce answer to question **What the cached locator is for?**
