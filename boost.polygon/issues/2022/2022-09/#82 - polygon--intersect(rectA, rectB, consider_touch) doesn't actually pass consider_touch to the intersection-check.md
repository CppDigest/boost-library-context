# #82 - polygon::intersect(rectA, rectB, consider_touch) doesn't actually pass consider_touch to the intersection-check [Open]

> Username: nmz787-intel  
> Created at: 2022-09-16 00:54:08 UTC  
> Updated at: 2022-09-16 00:54:08 UTC  
> Url: https://github.com/boostorg/polygon/issues/82  

function is:  
boost::polygon::intersect(T1& a, const T2& b, bool consider_touch = true)  
  
docs on the function:  
https://www.boost.org/doc/libs/1_64_0/libs/polygon/doc/gtl_rectangle_concept.htm  
  
bug where the bool isn't passed to the actual intersection-check is:  
https://github.com/boostorg/polygon/blob/8ba35b57c1436c4b36f7544aadd78c2b24acc7db/include/boost/polygon/rectangle_concept.hpp#L645  
  
  
fix:  
change  
`if(intersects(rectangle, b)) {`  
to:  
`if(intersects(rectangle, b, consider_touch)) {`
