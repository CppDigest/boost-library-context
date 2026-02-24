# #183 Feature/geographic [Merged]

> Username: awulkiew  
> Created at: 2014-11-21 00:38:45 UTC  
> Updated at: 2014-12-15 11:53:05 UTC  
> Merged at: 2014-12-15 11:47:50 UTC  
> Closed at: 2014-12-15 11:47:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/183  

The purpose of this PR is to move an algorithm resolving vincenty inverse problem outside of the vincenty strategy.  
  
The goal was to add the algorithm a way of calculating not only distance but also forward and reverse azimuths and in the same time to reuse as much code as possible. I implemented a class performing a common part of the algorithm in the ctor and allowing to calculate desired result with member functions. This way it's possible to e.g. calculate the distance and azimuth without recalculating most of the intermediate values:  
  
```  
vincenty_inverse<CT> vi(lon1, lat1, lon2, lat2, spheroid);  
vi.distance();  
vi.azimuth12();  
```  
  
The next step is to move the vincenty and andoyer strategies out of the extensions. Since Vincenty's algorithm could be used one way or another in various strategies would it be a good idea to rename it (e.g. to `vincenty_distance`)? Or would you prefer to leave the name `vincenty` for a distance strategy and pick some suitable names for possible other strategies in the future?

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-11-25 00:39:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#issuecomment-64291437  

I moved `andoyer` and `vincenty` strategies from extensions and added `side_by_azimuth` CS-agnostic strategy which could replace `spherical_side` strategy in geographic CS to achieve more precise results.  
  
Should it be the default strategy for geographical side calculation or should `spherical_side` stay as default? Similar question regarding `vincenty` vs `andoyer`. IMO we should favor correctness before speed and therefore should use vincenty formula in calculations for geographic CS. Only if the user explicitly passed a different strategy he should be able to use less "correct" method.  
  
`side_by_azimuth` is the same as `side_by_cross_track` I picked different name to not change the original one since the new strategy must take a reference model, Spheroid for geographic CS or some dummy model for other CS. At least for now when sphere's radius isn't used in this strategy for spherical CS or some definition of a Plane for cartesian CS.  
  
I added `detail::azimuth()` algorithm which is a CS-agnostic replacement for `detail::course()`. I picked this name because it better describes the function. It's used in `side_by_azimuth` strategy.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-12-09 22:47:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#issuecomment-66373048  

@awulkiew Could you please wait a bit before merging this. I would like to go over the code.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2014-12-10 17:41:47 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21620815  

Somewhere in this file (either here or before each CS supported), I would add comments explaining what the azimuth is.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2014-12-10 17:46:29 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21621165  

Please do not qualify `atan` and `tan` with `::` (does not allow for Koenig lookup).  
Same for all math functions that we do not have proper support in `math` namespace.  
See also occurrences below.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2014-12-10 17:50:08 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21621486  

I cannot comment below, which is why I do it here.  
I am referring to line 46: why you use `get_radius<2>(geometry)`? I thought that a spheroid has only two axes, accessed by `get_radius<0>()` and `get_radius<1>()`?

---

## Comment 6

> Username: awulkiew  
> Created_at: 2014-12-10 18:13:02 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21623175  

I implemented it this way to access radius values for all axes in Sphere, Spheroid and Ellipsoid models in a uniform way. In particular in Spheroid B (minor axis) is a radius defined for cartesian axis Z, and A (major axis) for cartesian X and Y axes. In general, it's designed this way to allow passing a Spheroid into an algorithm handling Ellipsoids. E.g. this way it'd be possible to implement only 1 function converting points on Sphere/Spheroid/Ellipsoid to 3D cartesian space.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2014-12-11 17:50:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#issuecomment-66659054  

@mkaravel I fixed the issues mentioned by you.  
  
I also added an implementation on Vincenty's direct formula. It's used for testing of Vincenty's inverse formula to calculate the second point from resulting distance and azimuth. I also use it in the test visualizing spheroid and geo-segment/curves between 2 points obtained using various methods (https://github.com/awulkiew/test-geoside).

---

## Comment 8

> Username: mkaravel  
> Created_at: 2014-12-12 16:39:22 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21755622  

What is the reasoning behind `1e-12`?  
Could we make it dependent on the actual floating point type, and also dependent on machine epsilon?  
For user defined number types using the `long double`'s machine epsilon would be okay I guess (or maybe provide some other mechanism to define the error tolerance in this case).

---

## Comment 9

> Username: mkaravel  
> Created_at: 2014-12-12 16:40:50 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21755725  

Same question here as in direct Vincenty.

---

## Comment 10

> Username: mkaravel  
> Created_at: 2014-12-12 16:43:14 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21755936  

My understanding is that should be `mapping_geodetic`. For `mapping_geocentric` you do not need to compute a new latitude, you just use the geocentric one.

---

## Comment 11

> Username: mkaravel  
> Created_at: 2014-12-12 16:44:53 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21756113  

You probably mean geocentric here.  
It is the geocentric latitude that can be used directly, not the geocentric.

---

## Comment 12

> Username: awulkiew  
> Created_at: 2014-12-12 18:41:26 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21763759  

It's the other way around, check e.g.: http://en.wikipedia.org/wiki/Latitude#Geodetic_and_geocentric_latitudes

---

## Comment 13

> Username: awulkiew  
> Created_at: 2014-12-12 18:44:51 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21764027  

Same as above. The geocentric latitude is the one that needs to be calculated (http://en.wikipedia.org/wiki/Latitude#Auxiliary_latitudes).

---

## Comment 14

> Username: awulkiew  
> Created_at: 2014-12-12 19:53:30 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21768805  

AFAIU it's said that Vincenty's formula allows to calculate the distance with a millimeter accuracy. Sigma is an angle in radians and it's used later in calculations so I'm assuming it should be calculated with **at least** millimeter precision. On the equator (worst case scenario) 1e-12 more or less corresponds to 0.006mm (1mm~1e-10). I'm guessing that this threshold is some kind of empirical choice. We shouldn't choose value too small to not unnecessarily increase the number of iterations. So it's a precision vs performance tradeof.  
  
With that said I think that the true reason why this threshold was chosen is that it's mentioned on one of the pages in the comment (http://www.movable-type.co.uk/scripts/latlong-vincenty.html). On another one (http://futureboy.homeip.net/fsp/colorize.fsp?fileName=navigation.frink) 1e-6 arcsec is used which is more or less equal to 5e-12 rad if I'm correct. Btw, PostGIS implementation of Vincenty uses 1e-9.  
  
Yes, it'd be reasonable to use a threshold suitable for a floating point type used for calculation. I'm not sure if we should decrease it to achieve more precision but for sure we could consider using greater threshold for floats since the machine epsilon in this case is 10k times greater (~1e-8), so indeed we could use epsilon here for optimization purposes. Another solution would be to always use at least double and then cast the result back, as it's done for integral types.

---

## Comment 15

> Username: barendgehrels  
> Created_at: 2014-12-12 20:04:46 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21769579  

Thanks for the explanation!  
Agreed. Also with float - we could make the threshold much larger

---

## Comment 16

> Username: awulkiew  
> Created_at: 2014-12-12 20:19:49 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21770496  

@barendgehrels Actually I tried to explain why it was implemented this way (in the original version in extensions) but I'm not the author. It was added in this commit: https://github.com/boostorg/geometry/commit/eef963ea3e8c10fdf55f2d140e9fd1b65cc8de26 and I'm not sure how to access older history.

---

## Comment 17

> Username: barendgehrels  
> Created_at: 2014-12-12 21:33:48 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21774828  

Sure I understand - I adapted it around 2008/2009 from various sources, which are listed above the function.   
  
Your explanation is good that is why I commented.

---

## Comment 18

> Username: barendgehrels  
> Created_at: 2014-12-12 21:34:45 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21774900  

This is changed w.r.t. the implementation I wrote. Please revert it back to that - it is necessary for ttmath. It was actually noted there explicitly.  
  
So   
        // Oops getting hard here  
        // (again, problem is that ttmath cannot divide by doubles, which is OK)  
        CT const c47 = 47;  
        CT const c74 = 74;  
        CT const c128 = 128;  
        CT const c256 = 256;  
        CT const c175 = 175;  
        CT const c320 = 320;  
        CT const c768 = 768;  
        CT const c1024 = 1024;  
        CT const c4096 = 4096;  
        CT const c16384 = 16384;  
  
etc.

---

## Comment 19

> Username: barendgehrels  
> Created_at: 2014-12-12 21:36:57 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21775043  

Same here  
  
```  
    CT const c3 = 3;  
    CT const c4 = 4;  
    CT const c6 = 6;  
    CT const c16 = 16;  
  
    CT const c_e_12 = CT(1e-12);  
```  
  
Alternatively, you can try if it is compilable with ttmath now. We are several years further - maybe they have changed the implementation. But take care that ttmath is still supported - it is not part of the unit tests.

---

## Comment 20

> Username: barendgehrels  
> Created_at: 2014-12-12 21:38:54 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21775150  

OK - so this is probably what I did and the other source is what you added later?   
Then my question changes / not revert, because it did not exist before, but take care the new sources are also supported by ttmath...

---

## Comment 21

> Username: awulkiew  
> Created_at: 2014-12-13 02:13:38 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21785043  

Yes, the different approach is used in the new code (vincenty_direct). The moved one was untouched. Both works for (the old?) ttmath from extensions (after a fix).  
  
Though I wasn't be so sure that BG should work for ttmath in general. E.g. I was forced to fix haversine. side_by_cross_track still doesn't compile because when I tried to add a fix the test failed so I reverted it. In vincenty test I got very different results WRT results for doubles so I dug a little and what I found was that ttmath default constructor sets the object to NaN (it may be not the only reason). According to http://en.cppreference.com/w/cpp/language/value_initialization in C++03 for:  
  
```  
T n = T(); // default ctor in C++03, NOT 0  
```  
  
the default ctor is called. Therefore:  
  
```  
assert(n == T(0)); // FAILS for ttmath  
```  
  
In many places in the library it's assumed that the value-initialization sets the value to 0, which may be wrong for user-defined types. E.g. math::abs() implementation looks like this:  
  
```  
T const zero = T();  
return value < zero ? -value : value;  
```  
  
It doesn't work properly for ttmath. This is why we should explicitly initialize objects with values, e.g.:  
  
```  
T n1(0);  
T n3 = T(0);  
T n2 = 0;  
```  
  
Furthermore we shouldn't assume that any value-initialized object would contain zeros, e.g. an object of type model::point<> defining empty default ctor.  
  
This was just a side note. This PR is not a good place for fixing initializations.

---

## Comment 22

> Username: awulkiew  
> Created_at: 2014-12-13 03:21:07 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21785608  

Update, side_by_cross_track is also fixed.

---

## Comment 23

> Username: barendgehrels  
> Created_at: 2014-12-13 10:58:35 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21787576  

Thanks for updating the new code. My comment was mainly because I thought the old code was changed, but it was indeed a similar fragment. Sorry for the confusion.  
  
> Though I wasn't be so sure that BG should work for ttmath in general.   
  
Yes it is the intention that ttmath should work in general. Because in during review period this is one our the mentioned methods of tackling robustness.  
  
In the meantime we have more methods for robustness now, so relatively it is less important. But still, things which worked for ttmath should still work, that is why I made the comment. And besides that we should still let it work for new code too, though that has less priority.  
  
For example union/intersection also work (worked) with ttmath code. I should check it again, as said they are not automatically unit tested and my personal daily development platform is moved from Windows to Linux...

---

## Comment 24

> Username: awulkiew  
> Created_at: 2014-12-13 11:16:31 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21787635  

Hmm, what I intended to say was "I wasn't be so sure that BG indeed works for ttmath rigth now in all cases". It's clear that it should work for various user-defined numerical types. But since such types aren't tested by default we probably can't guarantee that. E.g. those 2 strategies mentioned above wasn't compiling and I didn't check other parts of the library...

---

## Comment 25

> Username: barendgehrels  
> Created_at: 2014-12-13 11:20:42 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21787655  

No, it does not work for ttmath right now in all cases indeed.  
I think in many recent developments it did not get much attention...

---

## Comment 26

> Username: mkaravel  
> Created_at: 2014-12-13 11:27:44 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21787686  

Okay, fine with me.

---

## Comment 27

> Username: mkaravel  
> Created_at: 2014-12-13 11:31:19 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21787702  

Here is what I have in mind: when you are given geocentric coordinates, then you can immediately use those in the spherical side formula. If you are given other coordinates (geodetic or reduced), then we need to map them to geocentric.  
What you do here is map geocentric to something else, which I do not understand. The formula you are using is for mapping geodetic to geocentric, which confuses me even more as to what you are trying to do.  
Could you please explain? I would also recommend adding comments to the source code to explain the idea behind these mappings. I still think that what you do should be done for geodetic coordinates, not geocentric.

---

## Comment 28

> Username: mkaravel  
> Created_at: 2014-12-13 11:39:37 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21787743  

ttmath was tweaked so that the default constructor initializes the value to 0. This is done in the `ttmath_big` class. Is that the one you are using for tests?  
I agree though with the principle that we should not expect the default constructor to initialize with 0. I am pretty sure that some parts of the library really depend on this assumption.

---

## Comment 29

> Username: awulkiew  
> Created_at: 2014-12-13 11:49:37 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21787786  

The geographic coordinates are assumed to be **geodetic** by default. To use geocentric or reduced latitude in spherical CS I need to calculate it from the geographic/geodetic latitude.  
  
(http://en.wikipedia.org/wiki/Latitude#Geodetic_and_geocentric_latitudes)  
  
> **Geodetic latitude:** (...) This is the definition assumed when the word latitude is used without qualification. (...)  
  
(http://en.wikipedia.org/wiki/Latitude#Geocentric_latitude)  
  
> The **geocentric latitude** (...) The relation between the geocentric latitude (ψ) and the geodetic latitude (φ) is derived in the above references as  
> [an equation]  
  
(http://en.wikipedia.org/wiki/Great_ellipse#Introduction)  
  
> - The ellipsoid can be stretched into a prolate ellipsoid with polar semi-axis a^2/b and then mapped radially onto the sphere; this **preserves the latitude**—the latitude on the sphere is \phi, the **geographic latitude**.  
>   
> The last method gives an easy way to generate a succession of way-points on the great ellipse connecting two known points A and B. Solve for the great circle between (\phi_1,\lambda_1) and (\phi_2,\lambda_2) and find the way-points on the great circle. **These map into way-points on the corresponding great ellipse**.

---

## Comment 30

> Username: awulkiew  
> Created_at: 2014-12-13 12:04:20 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21787857  

In the `strategies/vincenty.cpp` `ttmath::Big<1,4>` and `ttmath_big` are tested. I see, so ttmath_big was added in extensions to support zeroing default construction. Ok, we're going off-topic.

---

## Comment 31

> Username: barendgehrels  
> Created_at: 2014-12-13 12:06:19 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21787866  

Sure that is the only reason.  
Personally I think it is missing from ttmath. All types are constructable by () delivering 0, and we use it internally in the library indeed.

---

## Comment 32

> Username: barendgehrels  
> Created_at: 2014-12-13 12:09:07 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21787878  

Yes I think the mapping is correct.  
I only think map_lat should be called apply.  
And I think (in the future) we maybe should support both as coordinate-systems and call "convert", but for now it is fine as it is.

---

## Comment 33

> Username: barendgehrels  
> Created_at: 2014-12-13 12:11:15 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21787890  

Should mapping_geodetic not read Mapping?

---

## Comment 34

> Username: barendgehrels  
> Created_at: 2014-12-13 12:11:29 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21787892  

calculation_type

---

## Comment 35

> Username: barendgehrels  
> Created_at: 2014-12-13 12:13:35 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21787899  

So mapper is declared for each apply.  
I understand the reason (calc_t) if we could declare it in the class we save many divisions.

---

## Comment 36

> Username: barendgehrels  
> Created_at: 2014-12-13 12:15:39 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21787906  

ResultPoint, Point, source_point (or just point), destination_point (or just result)

---

## Comment 37

> Username: barendgehrels  
> Created_at: 2014-12-13 12:18:10 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21787922  

What is unclear to me is why this specialization exists. If it does not exist, it does exactly the same right? Because mapping just returns the passed coordinates.

---

## Comment 38

> Username: awulkiew  
> Created_at: 2014-12-13 12:34:26 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21787981  

I was considering using calculation_type as coordinate type of mapped points (in the comment), to e.g. don't convert ints to doubles and then again back to ints. In this case additional point object would be needed so to not do it I specialized the strategy. And the specialization of mapping<> was implemented for completness, though I agree that one of them is not needed.

---

## Comment 39

> Username: awulkiew  
> Created_at: 2014-12-13 12:37:53 UTC  
> Updated_at: 2014-12-15 02:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#discussion_r21787990  

Of course it should!  
  
Yes, and now it gives different results. But I remember that SSF doesn't use math::equals/epsilon so maybe using it will fix that.

---

## Comment 40

> Username: awulkiew  
> Created_at: 2014-12-13 15:55:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/183#issuecomment-66880657  

Ok, the problem was related with the conversion degree<->radian (the lack of it) when the mapping was performed.  
  
I also removed the creation of intermediate mapped points. Now the coordinates are directly passed to the SSF formula which was moved outside SSF strategy. This should be more precise since the calculation_type is used from the beginning.  
  
I also moved the mapper outside the ssf::apply() method as you suggested. For storing fraction I used Spheroid's radius_type promoted to double if necessary. So it should play nice with CalculationType.

---
