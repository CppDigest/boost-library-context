# #1278 - Cannot make 'point_on_surface' compile [Closed]

> Username: JD31  
> Created at: 2024-05-03 14:26:52 UTC  
> Updated at: 2024-05-17 13:50:31 UTC  
> Closed at: 2024-05-17 13:50:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/1278  

I don't manage to use 'point_on_surface'. (Maybe I don't use the right method??)     
  
```   
    typedef boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian> BGPoint;  
    boost::geometry::model::polygon<BGPoint> poly;  
  
    // ...  
  
    BGPoint point;  
    boost::geometry::point_on_surface(poly, point);  
```  
  
It does not build due to a static assert :  
  
> In template: static assertion failed due to requirement 'boost::geometry::detail::static_assert_check<false, boost::geometry::cartesian_tag>::value': Not implemented for this type.    
  
> in instantiation of template class 'boost::geometry::strategy::side::services::default_strategy<boost::geometry::cartesian_tag>' requested here in instantiation of function template specialization 'boost::geometry::point_on_surface<boost::geometry::model::polygon<boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>>, boost::geometry::model::point<double, 2, ...

---

## Comment 1

> Username: vissarion  
> Created at: 2024-05-16 12:33:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/1278#issuecomment-2115131083  

Sorry, I cannot reproduce it. Could you please provide more information? That is, all the code you tried to compile. For example what headers are you including?

---

## Comment 2

> Username: JD31  
> Created at: 2024-05-17 13:08:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/1278#issuecomment-2117567960  

Here is a Goldbolt link to an example that does not compile and with all the includes I use (lot are not necessary but it is representative of my build context):  
https://godbolt.org/z/PK86ocnbc

---

## Comment 3

> Username: vissarion  
> Created at: 2024-05-17 13:28:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/1278#issuecomment-2117611238  

You have to include also   
```  
#include <boost/geometry/strategies/strategies.hpp>  
```  
  
alternatively replace all boost geometry includes by `#include <boost/geometry.hpp>`.

---

## Comment 4

> Username: JD31  
> Created at: 2024-05-17 13:50:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/1278#issuecomment-2117655043  

Right. Thank you.
