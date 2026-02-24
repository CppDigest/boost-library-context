# #1370 Relax half period check in spheroidal normalization [Merged]

> Username: vissarion  
> Created at: 2025-01-30 14:08:40 UTC  
> Updated at: 2025-03-18 13:33:27 UTC  
> Merged at: 2025-03-18 12:41:07 UTC  
> Closed at: 2025-03-18 12:41:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1370  

Currently when points (180,0) and (-180,0) are tested for equality (in spherical or geographic systems) we are getting the expected result which is true.   
  
However, if the points are in radians instead of degrees the accuracy of the representation of pi/2 or the conversion from 180 degrees to radians could result in wrong results of equals (see added tests in this PR for details).   
  
This PR fixes this issue by relaxing conditions in normalization of spheroidal coordinates.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-30 17:05:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1370#pullrequestreview-2584511939  

📁 include/boost/geometry/util/normalize_spheroidal_coordinates.hpp

```diff
 242 |-         if (math::equals(math::abs(longitude), constants::half_period()))
 243 |+         CoordinateType const epsilon = CoordinateType(1e-07);
 244 |+         bool const is_integer = std::numeric_limits<CoordinateType>::is_integer;
```

> Username: barendgehrels  
> Created_at: 2025-01-30 17:05:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/1370#discussion_r1935970531  

Usually we use `is_integral`

> Username: tinko92  
> Created_at: 2025-02-01 07:35:24 UTC  
> Updated_at: 2025-02-01 08:57:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1370#discussion_r1938221678  

Interesting. According to cppreference   
> If the program adds specializations for std::is_integral or std::is_integral_v, the behavior is undefined.  
https://en.cppreference.com/w/cpp/types/is_integral  
  
but   
  
> Implementations may provide specializations of std::numeric_limits for implementation-specific types: e.g. GCC provides std::numeric_limits<__int128>. Non-standard libraries may [add specializations](https://en.cppreference.com/w/cpp/language/extending_std) for library-provided types, e.g. [OpenEXR](http://openexr.com/) provides std::numeric_limits<half> for a 16-bit floating-point type.  
https://en.cppreference.com/w/cpp/types/numeric_limits  
  
Given the design objective of genericity and the use of e.g. boost multiprecision in tests and it showing up in issues, maybe numeric_limits is the more appropriate choice. (Edit: Also discussed here https://github.com/boostorg/multiprecision/issues/230)

> Username: tinko92  
> Created_at: 2025-02-01 14:50:53 UTC  
> Updated_at: 2025-02-01 15:34:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1370#discussion_r1938287872  

Also, this constant can be constexpr to document that it is compile-time static.

> Username: vissarion  
> Created_at: 2025-02-04 14:32:10 UTC  
> Updated_at: 2025-02-04 14:32:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/1370#discussion_r1941287368  

Here, we need `is_integer` to be true even for not native types that have the "behavior" of integers. We may have to revisit other places where `is_integral` is used in the library.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-30 17:07:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1370#pullrequestreview-2584517859  

📁 include/boost/geometry/util/normalize_spheroidal_coordinates.hpp

```diff
 244 |+         bool const is_integer = std::numeric_limits<CoordinateType>::is_integer;
 245 |+ 
 246 |+         if (exact || is_integer ? math::equals(math::abs(longitude), constants::half_period())
```

> Username: barendgehrels  
> Created_at: 2025-01-30 17:07:10 UTC  
> Updated_at: 2025-01-30 17:07:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/1370#discussion_r1935973686  

`math::equals` is still not exact for floating point.  
I'm not sure of the context, but the `1.e-7` looks a bit rough and arbitrary.

> Username: vissarion  
> Created_at: 2025-02-04 14:29:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/1370#discussion_r1941281560  

Yes, it is indeed, I am not happy with this either.   
  
Selecting the right epsilon value depends on the threshold we want to set when declaring two points with longitude 180 to be equal. `1.e-7` is the smallest value so that the new tests are passing, actually set by [this test](https://github.com/boostorg/geometry/pull/1370/files#diff-6898dddc1f86c9a558e1a3871bf25eebfa1b492dcb7a0e7c7de1c86a78e400b5R200); if we only want to support [this test case](https://github.com/boostorg/geometry/pull/1370/files#diff-6898dddc1f86c9a558e1a3871bf25eebfa1b492dcb7a0e7c7de1c86a78e400b5R204)  a smaller value suffices.   
The latter case seems important since it could be the case the 180 degrees is converted to radian using the constants of the spheroid (that is the case of mysql).

> Username: vissarion  
> Created_at: 2025-02-04 14:29:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1370#discussion_r1941283271  

In any case, if we keep such an arbitrary value the selection should be documented in a comment.


---

## Review 3 [Approved]

> Username: barendgehrels  
> Created_at: 2025-01-30 17:12:12 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1370#pullrequestreview-2584530731  

I'm ok with some questions.  
Thanks!

---

## Comment 4

> Username: vissarion  
> Created_at: 2025-03-05 11:13:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/1370#issuecomment-2700611818  

@barendgehrels @tinko92 are you OK with this PR? I would like to be included in 1.88.  
The main question is related to the threshold epsilon value (see inline discussion).

---

## Comment 5

> Username: tinko92  
> Created_at: 2025-03-05 15:40:20 UTC  
> Updated_at: 2025-03-06 01:34:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1370#issuecomment-2701323266  

I am okay with merging and setting the epsilon to `std::numeric_limits<float>::epsilon();` to make the intent clear to return half_period for longitudes that are given as single precision constants, as in the test that requires the large threshold. If that is not the intent, I would change the threshold to the longitude error that is desired, at least 1e-14 to support the two remaining test cases (which are not at but close to the limit of double precision). I included more detailed discussion inline. Also added a minor note to the serialisation with precision function.

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2025-03-05 17:48:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/1370#issuecomment-2701656373  

I'm OK, I missed some of the follow-up indeed.   
For me fine to merge it.

---
