# #52 - Degree definition causes quite big conversion error between degrees and radians. [Open]

> Username: Razumihin  
> Created at: 2021-10-07 07:32:24 UTC  
> Updated at: 2021-10-07 07:32:24 UTC  
> Url: https://github.com/boostorg/units/issues/52  

I just noticed that the degree definition in `boost/units/base_units/angle/degree.hpp` uses quite inaccurate value:  
  
```  
BOOST_UNITS_DEFINE_BASE_UNIT_WITH_CONVERSIONS(angle,degree,"degree","deg",6.28318530718/360.,boost::units::angle::radian_base_unit,-101);  
```  
  
as units is meant for scientific calculation I'd think that it would be a great benefit to use the highest possible accuracy for the definition. Right now the accuracy will cause significant error over time, when for instance when stepping an integration that uses this conversion for the angles.   
  
It seems like units doesn't have it's own constants definitions, so maybe we should just use the value from the math/constants here?
