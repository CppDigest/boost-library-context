# #1140 - DOC: Missing factor of 1/pi in the Cauchy CDF formula. [Closed]

> Username: WarrenWeckesser  
> Created at: 2024-05-22 19:37:50 UTC  
> Updated at: 2024-08-23 18:49:44 UTC  
> Closed at: 2024-08-23 18:49:44 UTC  
> Url: https://github.com/boostorg/math/issues/1140  

In this web page  
  
https://www.boost.org/doc/libs/1_85_0/libs/math/doc/html/math_toolkit/dist_ref/dists/cauchy_dist.html#math_toolkit.dist_ref.dists.cauchy_dist.implementation  
  
the alternative formulation for the CDF of the Cauchy distribution is written  
  
$p = -atan(1/x)$  
  
but it should be  
  
$p = -atan(1/x)/\pi$  
  
The code has the factor of 1/pi; it is only missing in the documentation.  
  
There are three places where this occurs:  
* https://github.com/boostorg/math/blob/35612734665edf68e8716718668d2e85314b1380/doc/distributions/cauchy.qbk#L119  
* https://github.com/boostorg/math/blob/35612734665edf68e8716718668d2e85314b1380/doc/html/math_toolkit/dist_ref/dists/cauchy_dist.html#L224 (maybe derived from the .qbk file?)  
* https://github.com/boostorg/math/blob/35612734665edf68e8716718668d2e85314b1380/include/boost/math/distributions/cauchy.hpp#L50
