# #780 Fix/sort by side point at turn [Merged]

> Username: barendgehrels  
> Created at: 2020-12-02 13:40:54 UTC  
> Updated at: 2021-06-02 09:47:50 UTC  
> Merged at: 2020-12-30 10:37:24 UTC  
> Closed at: 2020-12-30 10:37:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/780  

Fixes number of remaining cases (in `recursive_polygons_buffer`) from 1341 to 628 (walking backwards)  
and then from 628 to 444 (walking forwards)  
  
It avoids sorting segments around a turn when the segment itself is at the turn (near-zero length)  
It adds a new unit test for `copy_segment_points` because I added negative offsets for that.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2020-12-02 13:42:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/780#pullrequestreview-542845012  

📁 include/boost/geometry/algorithms/detail/overlay/sort_by_side.hpp

```diff
 326 |+         // walks max 1 point back in all cases.
 327 |+         while (approximately_equals(point1, turn.point, 1.0e-6) && offset > -10)
 328 |+         {
```

> Username: barendgehrels  
> Created_at: 2020-12-02 13:42:01 UTC  
> Updated_at: 2020-12-16 13:43:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/780#discussion_r534174705  

This loop is the fix.

> Username: awulkiew  
> Created_at: 2020-12-02 17:16:51 UTC  
> Updated_at: 2020-12-16 13:43:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/780#discussion_r534341551  

What is this number `1.0e-6`? Will it work for `float` too? Will it work for big point coordinates when the closest next or previous floating point number is greater than `1.0e-6` ? Shouldn't it be scaled by coordinates like epsilon or maybe even based on machine epsilon?

> Username: barendgehrels  
> Created_at: 2020-12-02 18:33:47 UTC  
> Updated_at: 2020-12-16 13:43:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/780#discussion_r534392570  

I wrote that in the comments. Machine epsilon won't work (I tried), it's too small and will still result in non-distinguishable sides (those are only indirectly based on these coordinates) - using them for side calculations afterwards.  
  
Your concern about big coordinates might be valid, I have to check that.

> Username: awulkiew  
> Created_at: 2020-12-02 19:56:13 UTC  
> Updated_at: 2020-12-16 13:43:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/780#discussion_r534442279  

I mean **based on** machine epsilon. So taking both of my remarks into account e.g.:  
```  
n * epsilon * max_coordinate(turn.point)  
```  
where `n` is your constant for tweaking. As an alternative `approximately_equals` could check relatively to coordinates because AFAIU right now `1.0e-6` is absolute value. So it could take epsilon-based-error, e.g. `n * epsilon` and then internally use this value together with each one coordinate to check for approximate equality. So use separate error-thresholds for each coordinate, basically similar to `equals()` but with epsilon multiplied by some factor.  
  
Side note: there are only 3 or 4 values of machine epsilon used in Boost.Geometry for a given machine. One for each floating point number or `0` for integers and user-defined types. So the above would not work well for user-defined floating point types unless `numeric_limits` are specialized for such type. And this is not always possible.

> Username: barendgehrels  
> Created_at: 2020-12-02 21:46:12 UTC  
> Updated_at: 2020-12-16 13:43:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/780#discussion_r534502445  

Thanks @awulkiew !  
I get the point and will apply it as you suggest.

> Username: barendgehrels  
> Created_at: 2020-12-09 11:31:17 UTC  
> Updated_at: 2020-12-16 13:43:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/780#discussion_r539229634  

I'm afraid this doesn't work for the range of values I'm using.  
  
For `double`: 1.0e9 * epsilon is 2.22045e-07, this is around the value I need.  
For `float`: 1.0e9 * epsilon is 119.209 and of course we can't work with that limit...  
For `long double`: 1.0e9 * epsilon is 1.0842e-10, this works still fine.  
  
I can use it if I use the double epsilon for float, which is kind of awkward.  
  
My implementation (but I won't push it):  
```  
    // Returns true if two points are approximately equal, tuned by a giga-epsilon constant  
    // (if constant is 1.0, for type double, the boundary is about 1.0e-6)  
    template <typename Point1, typename Point2, typename T>  
    static inline bool approximately_equals(Point1 const& a, Point2 const& b, T giga_epsilon)  
    {  
        // Including distance would introduce cyclic dependencies.  
        using coor_t = typename select_coordinate_type<Point1, Point2>::type;  
        constexpr coor_t epsilon = std::numeric_limits<coor_t>::epsilon();  
  
        coor_t const& a0 = geometry::get<0>(a);  
        coor_t const& b0 = geometry::get<0>(b);  
        coor_t const& a1 = geometry::get<1>(a);  
        coor_t const& b1 = geometry::get<1>(b);  
        coor_t const one = 1.0;  
        auto const c = math::detail::greatest(a0, b0, a1, b1, one);  
        auto const limit = 1.0e9 * giga_epsilon * epsilon * c;  
  
        return std::abs(a0 - b0) <= limit && std::abs(a1 - b1) <= limit;  
    }  
  
```  
  
And I call it with   
`while (approximately_equals(point1, turn.point, 1.0) && offset > -10)`

> Username: barendgehrels  
> Created_at: 2020-12-09 11:57:26 UTC  
> Updated_at: 2020-12-16 13:43:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/780#discussion_r539246165  

OK, using a `min` I can fix it. I will submit this.

> Username: barendgehrels  
> Created_at: 2020-12-09 12:42:46 UTC  
> Updated_at: 2020-12-16 13:43:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/780#discussion_r539274764  

:heavy_check_mark:

> Username: vissarion  
> Created_at: 2020-12-23 09:58:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/780#discussion_r547874980  

OK, so now `limit` is in the order of `1.0e-3`, `1.0e-7`, `1.0e-10`, for `float`, `double`, `long double` resp.   
  
Does the current solution work well (or better than the original with fixed `1.0e-6`) for large coordinates? What about user defined floating point numbers?   
  
A similar question, is `limit_giga_epsilon` ever needed to be different than `1.0`?

> Username: barendgehrels  
> Created_at: 2020-12-23 12:17:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/780#discussion_r547930833  

No, the solution doesn't work better, it works about the same (there's one case different for long double which I still have to investigate but I think it's not really related).  
  
However, I didn't test with huge coordinates (maybe I should) or very very small coordinates. These testcases have values in the range of -100 .. 100. And Adam pointed out that it might turn out differently for other ranges, and therefore this solution is probably better.  
  
About the limit: `1.0` works fine. But the limit is not crucial here: 2.0 will also work fine. Because it walks forwards or backwards to the next coordinate, and that will be fine unless there is a turn within the range of that limit...


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2020-12-02 13:43:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/780#pullrequestreview-542846207  

📁 test/algorithms/set_operations/difference/difference.cpp

```diff
 626 |     // The difference algorithm can generate (additional) slivers
 627 |-     BoostGeometryWriteExpectedFailures(10, 11, 24, 15);
 627 |+     BoostGeometryWriteExpectedFailures(10, 11, 24, 16);
```

> Username: barendgehrels  
> Created_at: 2020-12-02 13:43:17 UTC  
> Updated_at: 2020-12-16 13:43:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/780#discussion_r534175614  

I still have to check if this was a regression (and then: why) or something else.

> Username: barendgehrels  
> Created_at: 2020-12-09 11:51:47 UTC  
> Updated_at: 2020-12-16 13:43:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/780#discussion_r539242645  

It fails in another (better) way and therefore the #expectation differs (one test can have multiple failures, for area, number of output polygons, etc) :heavy_check_mark:


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2020-12-02 17:09:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/780#pullrequestreview-543055547  

📁 include/boost/geometry/algorithms/detail/overlay/copy_segment_point.hpp

```diff
  70 |+         while (offset < 0) { offset += segment_count; }
  71 |+ 
  72 |+         signed_size_type const target = (seg_id.segment_index + offset) % segment_count;
```

> Username: awulkiew  
> Created_at: 2020-12-02 17:09:42 UTC  
> Updated_at: 2020-12-16 13:43:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/780#discussion_r534336581  

It looks like it should be possible to calculate that without any loops and maybe even with one modulo operation.  
Can `-offset` be greater than `segment_count`?

> Username: barendgehrels  
> Created_at: 2020-12-02 18:30:56 UTC  
> Updated_at: 2020-12-16 13:43:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/780#discussion_r534390874  

Yes, modulo is probably possible. Offset is usually 1 (I've never encountered higher values)

> Username: barendgehrels  
> Created_at: 2020-12-09 12:43:13 UTC  
> Updated_at: 2020-12-16 13:43:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/780#discussion_r539275075  

Fixed :heavy_check_mark:


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2020-12-02 17:10:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/780#pullrequestreview-543056062  

📁 include/boost/geometry/algorithms/detail/overlay/copy_segment_point.hpp

```diff
  72 |+         signed_size_type const target = (seg_id.segment_index + offset) % segment_count;
  73 |+ 
  74 |+         geometry::convert(*(boost::begin(view) + target), point);
```

> Username: awulkiew  
> Created_at: 2020-12-02 17:10:17 UTC  
> Updated_at: 2020-12-16 13:43:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/780#discussion_r534337010  

Or `range::at(view, target)`.

> Username: barendgehrels  
> Created_at: 2020-12-02 18:31:10 UTC  
> Updated_at: 2020-12-16 13:43:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/780#discussion_r534391050  

Good idea, will change this

> Username: barendgehrels  
> Created_at: 2020-12-09 12:47:34 UTC  
> Updated_at: 2020-12-16 13:43:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/780#discussion_r539277878  

:heavy_check_mark:


---

## Comment 5

> Username: barendgehrels  
> Created_at: 2020-12-16 09:44:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/780#issuecomment-745999898  

I still had to use `(std::min)`.  
But now it's ready, and review comments are processed. Are you OK with this PR?

---

## Review 6 [Commented]

> Username: barendgehrels  
> Created_at: 2020-12-16 13:45:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/780#pullrequestreview-553698313  

📁 include/boost/geometry/algorithms/detail/overlay/sort_by_side.hpp

```diff
 353 |         {
 347 |-             point1 = walk_back(op, --offset, geometry1, geometry2);
 354 |+             point_to = walk_over_ring(op, ++offset, geometry1, geometry2);
```

> Username: barendgehrels  
> Created_at: 2020-12-16 13:45:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/780#discussion_r544309352  

Like for `from`, we should also walk over the ring for `to`  
(this is testcase `nores_wt_2`)


---

## Comment 7

> Username: barendgehrels  
> Created_at: 2020-12-22 18:28:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/780#issuecomment-749706782  

I want to continue tomorrow with the follow up.  
@awulkiew @vissarion are you OK with this PR?

---

## Review 8 [Approved]

> Username: vissarion  
> Created_at: 2020-12-23 09:58:29 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/780#pullrequestreview-557764100  

In general I am ok with merging.

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2020-12-30 10:37:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/780#issuecomment-752410785  

I will merge this, next PR will be built on top of this. If you've still concerns let me know and we can fix that afterwards.

---
