# #1064 - Bug in formulas/karney_direct.hpp [Closed]

> Username: arbitrary311  
> Created at: 2022-10-11 20:07:01 UTC  
> Updated at: 2022-11-16 13:04:56 UTC  
> Closed at: 2022-11-16 13:04:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/1064  

Problem was found doing densify operations on Geographic coordinates using Karney's strategy:  
  
>   
> bg::srs::spheroid<double> spheroid2(r_a, r_b);  
> SRSType spheroid;  
> bg::strategy::densify::geographic<StrategyKarney>   karney(spheroid);  
> double dRes = 50000.;  
> bg::densify(fov, DK, dRes, karney);  
>   
Karney's code expects angles to be input as degrees, however, they are input as radians in the Boost adaptation. This is already accounted for in karney_inverse.hpp, but it was not in karney_direct.hpp.   
  
https://github.com/boostorg/geometry/blob/ca14e7840f73ceda4ee70304040774379f0d43e9/include/boost/geometry/formulas/karney_inverse.hpp#L125-L129  
  
  
I corrected this and tested with the following changes:  
  
https://github.com/boostorg/geometry/blob/ca14e7840f73ceda4ee70304040774379f0d43e9/include/boost/geometry/formulas/karney_direct.hpp#L85-L88  
>   
> static CT const pi = math::pi<CT>();  
> static CT const d2r = math::d2r<CT>();  
> static CT const r2d = math::r2d<CT>();  
> result_type result;  
>   
> CT lon1 = lo1 * r2d;  
> CT const lat1 = la1 * r2d;  
>   
> Azi azi12 = azimuth12 * r2d;  
>   
  
https://github.com/boostorg/geometry/blob/ca14e7840f73ceda4ee70304040774379f0d43e9/include/boost/geometry/formulas/karney_direct.hpp#L171-L174  
>   
> result.reverse_azimuth = atan2(sin_alpha2, cos_alpha2);  
> // Convert the angle to radians.  
> // Note: Already in radians  
> //result.reverse_azimuth /= math::d2r<CT>();  
>   
  
https://github.com/boostorg/geometry/blob/ca14e7840f73ceda4ee70304040774379f0d43e9/include/boost/geometry/formulas/karney_direct.hpp#L185-L186  
>   
> // Convert the coordinate to radians.  
> // Note: Already in radians  
> //result.lat2  /= math::d2r<CT>();  
>   
  
https://github.com/boostorg/geometry/blob/ca14e7840f73ceda4ee70304040774379f0d43e9/include/boost/geometry/formulas/karney_direct.hpp#L210-L212  
>   
> // Convert to radians to get the  
> // longitudinal difference.  
> // Note: Already in radians -> need degrees  
> CT lon12 = lam12 * r2d;  
>   
  
https://github.com/boostorg/geometry/blob/ca14e7840f73ceda4ee70304040774379f0d43e9/include/boost/geometry/formulas/karney_direct.hpp#L226-L227  
>   
> math::normalize_longitude<degree, CT>(result.lon2);  
> // Now put back in radians  
> result.lon2 *= d2r;  
>   
  
Images below are before and after the corrections.  
  
![image](https://user-images.githubusercontent.com/90473413/195186530-c07e1420-1023-4c60-a482-9e4000aa3103.png)  
  
  
![image](https://user-images.githubusercontent.com/90473413/195186566-aaf7cf3a-194a-45a5-b7b2-4b218eb8fe4f.png)

---

## Comment 1

> Username: vissarion  
> Created at: 2022-10-24 10:02:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/1064#issuecomment-1288769069  

Thanks for the report. Would you like to open a pull request with your fix?

---

## Comment 2

> Username: arbitrary311  
> Created at: 2022-11-09 14:27:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/1064#issuecomment-1308848771  

@vissarion I apologize for the delayed response. I'm going to look at it in more detail this weekend. Right now I'm assuming that every call made to these two functions will pass the coordinates as radians, but I want to verify that. osgEarth deals with this common problem with an Angle class that you can get the value from as either degrees or radians as needed, but here (and likewise elsewhere) these coordinates are passed in as a singular value type (e.g., double) for X (longitude) and Y (latitude).  
  
https://github.com/gwaldron/osgearth/blob/f90f350ba18883127399bf7514326780ab297446/src/osgEarth/Units#L327-L336  
  
I also want to verify the accuracy of the implementation compared to Karney's GeographicLib itself. I've noticed some variations in values from some of the Math functions used here versus Karney's Math functions. Karney put a lot of effort into achieving very-high numerical precision with careful implementations of key constants (e.g., how he computes flattening of the ellipsoid) that aren't replicated in the Boost implementation. I should do a proper review of the code before opening a pull request, but if it seems like it'll be too much effort to make additional changes, I'll just open one pull request for the units issue and work on the rest later.  
  
https://github.com/geographiclib/geographiclib/blob/adfc2eb0bcbee9a56e2a7b069010da21ced3083f/include/GeographicLib/Constants.hpp#L137-L146

---

## Comment 3

> Username: vissarion  
> Created at: 2022-11-09 20:25:09 UTC  
> Updated at: 2022-11-09 20:25:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/1064#issuecomment-1309335515  

@arbitrary311 I proposed a fix while waiting for your answer (I was working on a similar issue with direct formulas). I just show your answer. My intention was to let you work on it! Sorry. However, please feel free to review my PR.
