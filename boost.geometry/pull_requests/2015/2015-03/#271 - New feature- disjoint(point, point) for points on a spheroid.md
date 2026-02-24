# #271 New feature: disjoint(point, point) for points on a spheroid [Merged]

> Username: mkaravel  
> Created at: 2015-03-14 00:46:40 UTC  
> Updated at: 2015-05-20 08:53:59 UTC  
> Merged at: 2015-05-13 12:11:35 UTC  
> Closed at: 2015-05-13 12:11:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/271  

This PR implements `bg::disjoint(point, point)` for points lying on a spheroid.  
Due to the dependency of `bg::equals()` on `bg::disjoint()` for points, `bg::equals(point, point)` and `bg::equals(segment, segment)` now also works for points and segments on a spheroid.  
  
The idea for checking point equality is to first normalize the coordinates (longitude and latitude) of the points, so that (assuming units are degrees):  
- Longitudes lie in the interval `(-180, 180]`.  
- Latitudes lie in the interval `[-90, 90]`.  
  
Once we have the normalized coordinates, we first check if both points are the north pole/south pole, otherwise we simply check their coordinates for equality.  
  
An interesting aspect of the way point equality is implemented is that no trigonometric functions are called, and if both points are measured in the same units, then no type explicit or implicit promotion is required (except for the very final step where the coordinates are checked against each other; at that point `math::equals()` internally selects the most precise type for its calculations).  
  
Normalizing the coordinates makes use of `operator%` for integers and `std::fmod()` for floating-point numbers. This is the motivation behind PR #269. This PR depends on that PR, and the unit test in this PR requires PR #269 to succeed.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-04-22 15:31:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#issuecomment-95238668  

So AFAIU currently latitudes outside `[-90, 90]` are treated as of points on the other side of the globe. I'm wondering if that's really needed. If we required that a latitude must be in `[-90, 90]` the normalization step would be simpler. Do you know about some use-case or domain where latitudes outside this range could be useful? Or a side effect of some technique/calculation which could produce such coordinates?  
  
An alternative could be:  
- checking the latitudes and throwing an exception if they were invalid,  
- ignoring the invalid latitudes,  
- truncating them to the `[-90, 90]`, because greater values could be unintentionally caused by some numerical errors during some calculations done before, in that case the longitude shouldn't be changed as you're proposing.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-04-22 16:48:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#issuecomment-95261392  

@awulkiew Your understanding is partially correct. If the latitude is outside `[-180, 180]` then it is immediately mapped to `[-180,180]`. If the latitude is in `[-180,-90) U (90, 180]` then it is indeed considered as on the other side of the globe. To normalize such points I modify both longitude and latitude.  
  
I am not aware of any application where latitudes outside the `[-90,90]` range are natively used. I was just trying to come up with a complete implementation. I do agree that if I did not have to normalize latitudes, then the whole normalization step would be a lot simpler (and twice as fast!).  
  
Among the alternatives you suggest I would prefer throwing an exception. It seems the most clean solution to me, and I would be more than happy to go in this direction. Let's try to reach a consensus on this.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2015-04-22 17:01:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#issuecomment-95264253  

Yes, throwing an exception seems to be a reasonable solution. But as I mentioned I fear that we could have to deal with the numerical errors from previous calculations. So maybe we could use some kind of hybrid solution like:  
  
```  
// for positive bound  
if ( lat > pi/2 )  
    if ( lat > pi/2 + threshold(lat) )  
        lat = pi/2;  
    else  
        throw domain_error_or_other();  
```  
  
`threshold()` could return e.g. `abs(lat) * numeric_limits<T>::epsilon()`. And of course consistently use this normalization routine in each function.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-04-22 17:05:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#issuecomment-95265500  

Wouldn't that be taken care of automatically by using `math::equals`, `math::larger` and `math::smaller`? I consistently use these in code under development.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2015-04-22 17:42:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#issuecomment-95280243  

Of course the comparison itself would work but if the function could throw an exception for a coordinate outside `[-90, 90]` then the exception would be thrown anyway. This is why I thought that we could check a little wider range.

---

## Comment 6

> Username: mkaravel  
> Created_at: 2015-04-23 07:34:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#issuecomment-95477122  

There are two issues I see here:  
1. numerical errors on the user's side than make the range `[-90,90]` a bit wider.  
2. numerical errors on the BG side that give latitude values outside the `[-90,90]` range.  
  
For problems of type (1) I think it is the user's responsibility to comply with the BG requirements, and so the user should normalize the latitude values before feeding them to BG. It is the user that should decide how much larger the range `[-90,90]` should be depending on the use case.  
  
For problems of type (2) I think using `math::equals` etc. on our side should be enough.

---

## Comment 7

> Username: mkaravel  
> Created_at: 2015-04-23 07:35:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#issuecomment-95477636  

About the exception name, would `latitude_out_of_range_exception` be okay?

---

## Comment 8

> Username: mkaravel  
> Created_at: 2015-04-23 07:43:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#issuecomment-95478827  

One more question: where do you think the exception should be thrown? I mean, where in our code should we throw it from?  
  
For example should `bg::read_wkt` throw this exception? Should it be on the algorithm's side?  
  
It seems obvious to me that `bg::equals(point, point)` and `bg::disjoint(point, point)` should throw this exception. Possibly other algorithms working on the spheroid, like `bg::envelope` and `bg::expand`, and possibly more.  
  
What do you think?

---

## Comment 9

> Username: awulkiew  
> Created_at: 2015-04-23 22:00:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#issuecomment-95731142  

Yes, `bg::equals()` should be enough. We could always make it bigger if the users were experiencing any problems.  
  
What do you think about making the name something more general and not so GIS-oriented? E.g. `coordinate_out_of_range` or `coordinate_out_of_range_exception`. There is `std::out_of_range` so maybe the first one? Or do we always use the word `exception` at the end of an exception type name in Geometry?  
  
It could be checked on the algorithm's side. In particular I was thinking about adding the check before the normalization of latitude (truncating to [-90, 90]). So this could be checked each time coordinates are normalized because AFAIU this would be done once per Point access. Is that reasonable?

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2015-04-24 18:24:06 UTC  
> Updated_at: 2015-04-28 18:33:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#discussion_r29072955  

We often used strategies for this (to distinguish between coordinate systems).   
But I think that dispatching it here is perfectly fine here.

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2015-04-24 18:26:33 UTC  
> Updated_at: 2015-04-28 18:33:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#discussion_r29073170  

2 PI happens more - also e.g. in projections - should we add it to math?

---

## Comment 12

> Username: barendgehrels  
> Created_at: 2015-04-24 18:29:29 UTC  
> Updated_at: 2015-04-28 18:33:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#discussion_r29073386  

Why is longitude here moved too?  
EDIT: OK I see - already explained and discussed by Adam

---

## Comment 13

> Username: barendgehrels  
> Created_at: 2015-04-24 18:37:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#issuecomment-96027530  

I like it and I'm OK with merging.  
  
W.r.t. either wrapping wrongly-ranged latitudes or throwing an exception, we normally only throw as the last resort - so in that case I would prefer the solution provided by Menelaos. But yes, I agree too, using lat=110 is quite uncommon. However, if longitudes wraps around, why would lat not wrap around...

---

## Comment 14

> Username: barendgehrels  
> Created_at: 2015-04-24 20:06:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#issuecomment-96048705  

W.r.t. wrapping latitudes: I think this can be a very useful tool for, for example, generating a buffer around a point or other geometry lying close to the north-pole. I don't yet know how this will be implemented in detail, but it might be that wrapping can be convenient there.

---

## Comment 15

> Username: awulkiew  
> Created_at: 2015-04-25 11:43:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#issuecomment-96181457  

@barendgehrels So you prefer wrapping the latitudes?  
  
My initial thought was to not throw an exception or wrap the latitude but only to truncate it into the [-90, 90].   
  
My greatest concern for the wrapping latitudes is that the longitude changes when this is done. This may cause some unwanted effects for some users and algorithms (because lat in [-90, 90] is in the definition of the spherical-equatorial CS so it's something that they can rely on in any way). On the opposite, the latitude doesn't change when the longitude is normalized.  
  
I think we should do more research before going forward. Search for the existing solutions and maybe ask the users on the list what's their preference.  
  
AFAIU it wouldn't be much difference for buffer, there should be a function for advancing in arbitrary direction on a sphere and the resulting coordinates would be correct, whatever they'd be.

---

## Comment 16

> Username: mkaravel  
> Created_at: 2015-04-25 15:48:05 UTC  
> Updated_at: 2015-04-28 18:33:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#discussion_r29101442  

Boost.Math defines quite a few of them. See  
http://www.boost.org/doc/libs/1_58_0/libs/math/doc/html/math_toolkit/constants.html  
  
My proposal would be to have (following the Boost.Math types) `math::half_pi<>` and `math::two_pi<>` along with the existing `math::pi<>`.  
I can add them to `util/math.hpp` as part of this PR if we agree.

---

## Comment 17

> Username: mkaravel  
> Created_at: 2015-04-25 15:49:46 UTC  
> Updated_at: 2015-04-28 18:33:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#discussion_r29101469  

Just be to clear: longitude measures angle from the prime meridian along the equator, latitude measures angle from equator along a meridian. When the latitude angle is more than 90 or less than -90 degrees, then to get a latitude in the range `[-90,90]` I also need to modify the longitude.

---

## Comment 18

> Username: mkaravel  
> Created_at: 2015-04-25 15:59:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#issuecomment-96232017  

My initial intention was to provide a waterproof implementation in the sense that no matter what the user enters as coordinates, then `bg::equals()` would work.  
  
The difference between longitude and latitude is that in the spherical CS (normalized) longitude values span an entire period, whereas latitude values span half a period, and in that sense wrapping is not so natural.  
  
FYI, MySQL allows longitude and latitude values in the ranges `(-180, 180]` and `[-90, 90]` respectively; values outside these ranges trigger an error. I wonder what other databases do.  
  
One solution that could possibly satisfy all users is to throw an exception for _illegal_ latitude values based on the definition of some macro. So if the macro is defined then an exception is thrown for out-of-range latitude values, whereas if the macro is not defined we do as currently in the PR. This way we can have both suggested behaviors. If on the application side the user knows that latitudes are in the proper range, then he/she can define the macro and benefit from the corresponding optimizations.

---

## Comment 19

> Username: mkaravel  
> Created_at: 2015-04-27 09:54:25 UTC  
> Updated_at: 2015-04-28 18:33:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#discussion_r29133842  

I decided to create another PR for these constants. See #284.

---

## Comment 20

> Username: mkaravel  
> Created_at: 2015-04-27 10:00:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#issuecomment-96589327  

After commit 9c50c6f this PR depends on PR #284, as it is now using the free functions `math::half_pi<>()` and `math::two_pi<>()`.

---

## Comment 21

> Username: mkaravel  
> Created_at: 2015-04-28 13:59:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#issuecomment-97073177  

@awulkiew @barendgehrels Do we have a consensus as to where to go with this PR?  
I can see the following options:  
1. Leave the code as it is now.  
2. Throw an exception for latitudes that are out of range.  
3. Follow an intermediate/hybrid approach between (1) and (2): introduce a macro that when defined an exception for out-of-range latitudes will be thrown, while if the macro is not defined the code would work as it is now.  
  
Let me know which one you prefer. Options (2) and (3) require more code to be written (most probably as part of this PR).

---

## Comment 22

> Username: awulkiew  
> Created_at: 2015-04-28 14:05:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#issuecomment-97074616  

I prefer to do the research and see how it's typically done (e.g. in other libraries) and maybe ask on the list.

---

## Comment 23

> Username: mkaravel  
> Created_at: 2015-05-11 12:53:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#issuecomment-100896977  

I downloaded [GeographicLib](http://geographiclib.sourceforge.net/html/index.html) and tried to see how they do it. If I understand things correctly, latitudes are always assumed to be in the range `[-90,90]`, whereas for longitudes the admissible range is `[-540,540)`. Longitudes seem to be normalized internally to the range `[-180,180)`.

---

## Comment 24

> Username: mkaravel  
> Created_at: 2015-05-13 10:13:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#issuecomment-101609638  

My concrete proposal here is to by default normalize longitude.  
Normalization of latitudes can be guarded by a macro, e.g., `BOOST_GEOMETRY_NORMALIZE_LATITUDE`.  
In the doc we should explicitly state that we expect longitudes in the range `[-540,540]` and latitudes in `[-90,90]`, and for values outside these ranges either wrong results may occur, or large numerical errors.

---

## Comment 25

> Username: barendgehrels  
> Created_at: 2015-05-13 11:19:45 UTC  
> Updated_at: 2015-05-13 11:19:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#issuecomment-101631079  

I agree with this proposal.  
For now, I would not yet add this macro/normalization of latitudes, as it is an improbable use case  
I'm OK with merging.

---

## Comment 26

> Username: awulkiew  
> Created_at: 2015-05-13 12:08:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#issuecomment-101642941  

I am personally in favor of simply assuming that the latitude is in range. But this is not a strong opinion so I'm ok with the proposal. I'll re-check it and merge. Thanks Menelaos!

---

## Comment 27

> Username: mkaravel  
> Created_at: 2015-05-13 14:27:07 UTC  
> Updated_at: 2015-05-13 14:27:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#issuecomment-101685657  

@awulkiew I am bit confused by the merge. I thought the consensus was to remove normalization for latitudes.  
It is okay though that you merged it, as PR #289 depends on this PR, so I can at least proceed with PR #289 more easily.  
In any case I will create another PR where normalization is performed for latitudes only.  
  
EDIT: Above I meant to write "for longitudes only".

---

## Comment 28

> Username: awulkiew  
> Created_at: 2015-05-13 14:54:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#issuecomment-101701362  

Ah sorry, I confused latitudes with longitudes :)  
Anyway, it's ok to do the change in a separate PR or in https://github.com/boostorg/geometry/pull/289.

---

## Comment 29

> Username: mkaravel  
> Created_at: 2015-05-20 08:53:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/271#issuecomment-103814775  

Comments raised in this PR (but not addressed before this PR was merged) have now been addressed in RP #289.

---
