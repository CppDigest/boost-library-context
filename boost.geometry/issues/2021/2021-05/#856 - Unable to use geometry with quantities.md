# #856 - Unable to use geometry with quantities [Closed]

> Username: fDarkShadow  
> Created at: 2021-05-25 13:04:55 UTC  
> Updated at: 2021-07-28 12:23:56 UTC  
> Closed at: 2021-07-28 12:23:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/856  

Hi,  
  
I tried to use quantities (angles) in geometry shapes, so I can use geometry algorithms. But in some source code, an int is declared to compute area, so it can't be used with quantities.  
  
An example is on Coliru : http://coliru.stacked-crooked.com/a/2b97d1e12d37f764  
  
Tried also with boost 1.76.0, problem still present (in a different file).

---

## Comment 1

> Username: mloskot  
> Created at: 2021-05-25 13:12:31 UTC  
> Updated at: 2021-05-25 13:19:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/856#issuecomment-847858391  

Copy of the sample to godbolt/clang https://godbolt.org/z/xv3xY7aP8  
an alternative to the gcc/coliru miserable output.  
  
------  
  
A side note, people should stop using MPL and start porting **away** from MPL :-)  
  
> Coincidentally, I just ran into a Boost.Units error testing with the new Ubuntu  
> beta release with Boost 1.67. Yet again, it's an MPL issue. MPL really needs to  
> disappear, it's totally unnecessary with variadic templates since C++11.  
  
~reasons behind [C++ Boost.Units - Survey on Lack of Adoption of UoM libraries](https://www.reddit.com/r/cpp/comments/b6wsyy/c_boostunits_survey_on_lack_of_adoption_of_uom/ejtdcgu/)

---

## Comment 2

> Username: mloskot  
> Created at: 2021-05-25 14:17:39 UTC  
> Updated at: 2021-05-25 15:56:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/856#issuecomment-847909315  

> But in some source code, an int is declared to compute area, so it can't be used with quantities.  
  
I think, this is not quite true or not an issue here. The `int` referenced above is this `2`:  
  
https://github.com/boostorg/geometry/blob/54f141779bd60a628a68f9b2f55910d9c01efdbe/include/boost/geometry/strategy/cartesian/area.hpp#L82-L86  
  
which translates to `radian two = 2`, but even if it is casted/commented away, it does not change the picture of the problem.  
  
------  
  
The problem seems to be 'computing' area calculation type for the `radian`.  
Here is how the Geometry deduces the types used in the area calculation:  
  
```cpp  
using ct_scalar = boost::geometry::select_calculation_type<point_scalar, point_scalar, void>::type;  
    // double  
using ct_quanty = boost::geometry::select_calculation_type<point_qty, point_qty, void>::type;  
    // radian  
  
// with the state patched temporarily to make return_type public  
using state_scalar = boost::geometry::strategy::area::cartesian<>::state<point_scalar>::return_type;  
    // double  
using state_quanty = boost::geometry::strategy::area::cartesian<>::state<point_qty>::return_type;  
    // radian  
```  
  
Instead, it looks like the `state_quanty` should be deduced to something like the [square_meter](https://www.boost.org/doc/libs/1_76_0/boost/units/systems/si/area.hpp), not the `radian`.  
  
I have zero experience with Boost.Units, but it looks like there are some type traits and selectors required somewhere in Boost.Geometry, perhaps as adoption/extension part.

---

## Comment 3

> Username: awulkiew  
> Created at: 2021-05-25 16:05:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/856#issuecomment-847999073  

Geometry is not tested with this use-case in mind. The library will probably not compile right now with Units. I don't know how much work it'd require to make them to work together.  
  
Regarding the example, adding to what @mloskot have said. Radian coordinate type is used together with cartesian CS. In this case the unit should rather be meters and then the result of area should be in square meters.  
  
Technically the units of the result are defined by both point's coordinate type and the strategy, in this case the default one.  
  
To give you the whole picture consider spherical/geographic CS. The coordinates are radians or degrees. This is defined by the CS passed into the `point` as template parameter (e.g. `bg::cs::geographic<bg::degree>`) so coordinate type would have to be consistent with that. The spherical/geographic area result is defined by the strategy or more precisely by the model of the CS passed into the strategy. In spherical it depends on the unit of sphere's radius and in geographic on the unit of spheroid's major and minor axes. If the spheroid defines axes in meters then the result of area is in square meters. By default sphere and spheroid are defined with `double`.  
  
> I have zero experience with Boost.Units, but it looks like there are some type traits and selectors required.  
  
If that's the case and it doesn't work automagically I'd probably oppose supporting Units because it'd add dependency. Unless someone made a strong case supporting that.  
  
I also don't have any experience with Units. For instance I have no idea why can't I assign 2 to variable representing radians. It probably has something to do with preventing implicit conversions. But as @mloskot mentioned this is a detail. The problem is a lot deeper than that.

---

## Comment 4

> Username: mloskot  
> Created at: 2021-05-25 19:24:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/856#issuecomment-848200289  

@awulkiew   
> I'd probably oppose supporting Units because it'd add dependency.  
  
Indeed. That's why I mentioned support via an extension, if at all.  
  
> why can't I assign 2 to variable representing radians.   
  
Indeed. I tried a few obvious gymnastics and none worked for me.

---

## Comment 5

> Username: barendgehrels  
> Created at: 2021-05-26 11:39:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/856#issuecomment-848697546  

> If that's the case and it doesn't work automagically I'd probably oppose supporting Units because it'd add dependency. Unless someone made a strong case supporting that.  
  
I agree with it. We should not become dependent on Boost.Units. If any, we should be less dependent on other Boost libraries (like Boost.Math did, they are now independent)  
  
Long ago I once tried to get it working, but I succeeded neither. But it's so long ago that I don't remember why not. But I remember that it was complex and intrusive.

---

## Comment 6

> Username: barendgehrels  
> Created at: 2021-07-28 12:23:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/856#issuecomment-888265863  

Closing this issue as per above reactions 2 months ago
