# #1027 Allow const inputs in buffer() [Merged]

> Username: awulkiew  
> Created at: 2022-06-28 14:04:02 UTC  
> Updated at: 2022-07-02 15:21:32 UTC  
> Merged at: 2022-06-29 20:55:02 UTC  
> Closed at: 2022-06-29 20:55:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1027  

I decided to use helper geometry with the same coordinate type instead of output ring type to avoid result changes in case different coordinate type was used in the output.  
  
This PR also fixes range emptiness check where member function of a range was called instead of `boost::empty()`.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2022-06-28 14:04:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1027#issuecomment-1168771130  

This is a fix for https://github.com/boostorg/geometry/issues/1025

---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2022-06-28 14:10:03 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1027#pullrequestreview-1021814368  

LGMT, thanks!

---

## Review 3 [Approved]

> Username: barendgehrels  
> Created_at: 2022-06-29 09:16:19 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1027#pullrequestreview-1023011073  

Agree with all! Thanks!

---

## Comment 4

> Username: awulkiew  
> Created_at: 2022-06-29 22:03:58 UTC  
> Updated_at: 2022-06-29 22:04:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/1027#issuecomment-1170538919  

This PR caused buffer to stop compiling for input linestrings which `point_type` is different than `model::point`:  
https://github.com/boostorg/geometry/runs/7120358803  
  
The reason is that `buffer_inserter<linestring_tag, ...>` defines points of type taken from input linestring:  
https://github.com/awulkiew/geometry/blob/develop/include/boost/geometry/algorithms/detail/buffer/buffer_inserter.hpp#L644-L645  
but `helper_geometry` linestring always uses `model::point`:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/geometries/helper_geometry.hpp#L47  
  
There are two fixes possible:  
1. Make these points `auto const&`, however then `end_strategy.apply()` has to take various point types, now it's capable of taking only one:  
https://github.com/awulkiew/geometry/blob/develop/include/boost/geometry/strategies/cartesian/buffer_end_round.hpp#L112  
2. Convert these points to `output_point_type`  
  
@barendgehrels which one do you prefer?

---

## Comment 5

> Username: awulkiew  
> Created_at: 2022-06-29 22:10:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1027#issuecomment-1170544960  

Note that `segment_strategy` is capable of taking only one point type as well but since it takes both input point types it compiles:  
https://github.com/awulkiew/geometry/blob/develop/include/boost/geometry/algorithms/detail/buffer/buffer_inserter.hpp#L660  
  
It is possible that there are parts of the code that also should fail for different input and output point types but are not tested.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2022-06-29 22:15:10 UTC  
> Updated_at: 2022-06-30 08:00:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1027#issuecomment-1170549592  

There is also a third fix possible:  
  
3. Always use `point_type<Input>::type` with helper linestring and ring. This would be backward compatible but it wouldn't be possible to use const points in input geometries. So I guess this is not an option.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2022-07-02 15:21:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/1027#issuecomment-1172915291  

4. Using `point_type<Output>::type` with helper linestring/ring solves the issue and at the same time doesn't force us to refactor buffer. The reason is that with the current implementation output point type is used inside and expected as parameters in various places in `buffer_inserter` and `buffered_piece_collection`.

---
