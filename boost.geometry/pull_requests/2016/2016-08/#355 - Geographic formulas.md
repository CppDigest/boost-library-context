# #355 Geographic formulas [Merged]

> Username: awulkiew  
> Created at: 2016-08-02 21:10:08 UTC  
> Updated at: 2016-09-30 13:59:05 UTC  
> Merged at: 2016-08-13 17:38:50 UTC  
> Closed at: 2016-08-13 17:38:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/355  

This PR changes/adds formulas which could be used in the implementation of algorithms and strategies for geometries in geographic CS. It's the result of experiments with various methods of solving geodesic problems. Below is the list of changes:  
- existing geographic formulas are moved from `algorithms/detail`/`bg::detail` to `formulas`/`formula` directory/namespace:  
  - `andoyer_inverse`  
  - `thomas_inverse`  
  - `vincenty_inverse`  
  - `vincenty_direct`  
- inverse and direct formulas in addition to distance/azimuth (inverse) and coordinates of the second point (direct) can now calculate reverse azimuth, reduced length and geodesic scale.  
- new formulas are added:  
  - `thomas_direct` - solution of direct geodesic problem with Andoyer-Lambert-Forsyth type of approximation with 2nd order terms in flattening as proposed by Thomas.  
  - `differential_quantities` - formula calculating reduced length and geodesic scale from segment endpoints and azimuths as proposed by Karney, it is used in inverse and direct formulas  
  - `sjoberg_intersection` - formula calculating the intersection of two geodesics as proposed by Sjoberg, it takes inverse formula and the order of approximation of geodesic integral as template parameters   
  - `gnomonic_spheroid` - gnomonic projection on spheroid (ellipsoid of revolution) as proposed by Karney, it takes inverse and direct formulas as template parameters  
  - `gnomonic_intersection` - formula calculating the intersection of two geodesics iteratively projecting, finding the intersection point and projecting back, as proposed by Karney, it takes inverse and direct formulas as template parameters  
  
Various formulas has different accuracy and performance.  
  
More testing has to be done but I think that the PR could be merged now because parts of it could be used in implementation of other formulas and strategies, e.g. differential quantities in the implementation of geographic area (as proposed by Karney).  
  
In the near future I plan to implement inverse, direct and intersection formulas based on great elliptic arcs (in cartesian 3d, so similar to what we have already for spherical). Some researchers claim that the results are better than in case of Andoyer's formula and the method is faster (_Tseng at al - A COMPARISON OF GREAT CIRCLE, GREAT ELLIPSE, AND GEODESIC SAILING, 2013_). Taking into account that currently we use Andoyer's formula in geographic distance by default a potentially better and simple alternative method could be the default for all geographic algorithms.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2016-08-03 06:20:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/355#issuecomment-237152428  

Thanks! Will comment later, hopefully today

---

## Comment 2

> Username: vissarion  
> Created_at: 2016-08-08 09:09:08 UTC  
> Updated_at: 2016-08-11 15:30:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/355#discussion_r73843481  

Should be CT(2) instead of 2. This should hold in general for all constants in the code in order to support other number types like ttmath.

---

## Comment 3

> Username: vissarion  
> Created_at: 2016-08-08 09:10:46 UTC  
> Updated_at: 2016-08-11 15:30:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/355#discussion_r73843750  

Instead of paper 2007, paper, etc it is more standard to use something like [Sjoberg02].

---

## Comment 4

> Username: vissarion  
> Created_at: 2016-08-08 09:12:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/355#issuecomment-238182450  

Thank you Adam for this useful PR! I had some minor comments. I am ok for merging for now, and test it more later.

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2016-08-10 08:30:30 UTC  
> Updated_at: 2016-08-11 15:30:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/355#discussion_r74204883  

Please either add parentheses, or putting it in one line is also OK for me

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2016-08-10 08:31:17 UTC  
> Updated_at: 2016-08-11 15:30:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/355#discussion_r74205004  

unsigned int? std::size_t ?

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2016-08-10 08:32:21 UTC  
> Updated_at: 2016-08-11 15:30:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/355#discussion_r74205136  

BOOST_ASSERT(one_minus_f != 0) (for dvisions below) ?

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2016-08-10 08:35:24 UTC  
> Updated_at: 2016-08-11 15:30:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/355#discussion_r74205545  

Please change l into len, because l/1 are difficult to distinguish - and we divide by it, how sure are we that it is non zero?

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2016-08-10 08:38:39 UTC  
> Updated_at: 2016-08-11 15:30:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/355#discussion_r74205990  

It is MIT-licensed, also open and we change license here to Boost. It is reworked to templates and Karney suggested this.  I don't know how to exactly formulate that here

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2016-08-10 08:39:10 UTC  
> Updated_at: 2016-08-11 15:30:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/355#discussion_r74206047  

small things, comma after space, and at template < the < should go to next line

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2016-08-10 08:39:51 UTC  
> Updated_at: 2016-08-11 15:30:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/355#discussion_r74206138  

Why? Why not just pass the variables? Are the types conversed?

---

## Comment 12

> Username: barendgehrels  
> Created_at: 2016-08-10 08:41:38 UTC  
> Updated_at: 2016-08-11 15:30:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/355#discussion_r74206378  

Non zero?

---

## Comment 13

> Username: barendgehrels  
> Created_at: 2016-08-10 08:55:47 UTC  
> Updated_at: 2016-08-11 15:30:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/355#discussion_r74208222  

std::size_t const

---

## Comment 14

> Username: barendgehrels  
> Created_at: 2016-08-10 09:04:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/355#issuecomment-238807898  

Thanks a lot Adam! This is a considerable extension of our geographic functionality. I'm OK with merging.

---

## Comment 15

> Username: barendgehrels  
> Created_at: 2016-08-10 09:04:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/355#issuecomment-238808013  

Do you target 1.62 for these enhancements?

---

## Comment 16

> Username: awulkiew  
> Created_at: 2016-08-10 12:16:14 UTC  
> Updated_at: 2016-08-11 15:30:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/355#discussion_r74232550  

I'll change it to `unsigned int` to avoid confusion.  
FYI `unsigned` is short for `unsigned int` (http://en.cppreference.com/w/cpp/language/types).

---

## Comment 17

> Username: awulkiew  
> Created_at: 2016-08-10 12:26:25 UTC  
> Updated_at: 2016-08-11 15:30:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/355#discussion_r74233824  

`1-f = 0 <=> f=1 <=> b=0` so it's a disk ("flat" ellipsoid). And `b` may be passed by the user so maybe it should be an exception. But since it's such unreasonable scenario it shouldn't be checked here each time a formula is used because in the vast majority of cases `f` will be reasonable. Instead it could be checked in `srs::spheroid`. What do you think?

---

## Comment 18

> Username: barendgehrels  
> Created_at: 2016-08-10 12:37:37 UTC  
> Updated_at: 2016-08-11 15:30:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/355#discussion_r74235197  

I know ;-)  
Thanks for changing

---

## Comment 19

> Username: barendgehrels  
> Created_at: 2016-08-10 12:38:40 UTC  
> Updated_at: 2016-08-11 15:30:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/355#discussion_r74235320  

Indeed the scenario is improbable but not impossible.  
OK for me to check it in spheroid, and add a comment here that it is checked there.

---

## Comment 20

> Username: awulkiew  
> Created_at: 2016-08-10 12:53:25 UTC  
> Updated_at: 2016-08-11 15:30:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/355#discussion_r74237477  

The `\author` is the author of the papers/formulas, not author of the code. I've taken this description template from Andoyer and Vincenty which was first formulas implemented in BG. And there the `\author` mentions papers containing the original formulas.  
  
I haven't used the code of GeographicLib directly which indeed is MIT licensed. However indeed I've seen the code and used it as a complementary source of formulas to the paper because some things were not obvious in the paper (in particular in case of differential quantities and gnomonic projection).  
  
This particular formula (intersection using gnomonic projection) is not implemented in GeographicLib. A simple example was attached by Karney in the forum thread but I haven't used it directly either (plus there was no license there).

---

## Comment 21

> Username: awulkiew  
> Created_at: 2016-08-10 12:57:21 UTC  
> Updated_at: 2016-08-11 15:30:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/355#discussion_r74238043  

They might be conversed if they were different than `CalculationType`.

---

## Comment 22

> Username: awulkiew  
> Created_at: 2016-08-11 15:39:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/355#issuecomment-239200258  

Done. Regarding 1.62, these formulas aren't used in any strategy/algorithm for now, they may need a few minor tweaks too, so it doesn't matter if they're released. However I know that Vissarion is working on geographic area strategy which use these formulas. So if he plans to propose a PR for 1.62 then these formulas should be released as well. I don't know if I'm going to implement geographic intersection strategy (I'm experimenting with a different approach now), there is one more week left before master branch is closed for major changes and week after that for bugfixes, so maybe.

---
