# #934 Replace check_iterator_range by std::any_of, std::all_of or std::none_of [Merged]

> Username: vissarion  
> Created at: 2021-11-09 15:50:09 UTC  
> Updated at: 2022-02-15 13:44:57 UTC  
> Merged at: 2022-02-15 13:44:54 UTC  
> Closed at: 2022-02-15 13:44:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/934  

This PR proposes the replacement of `check_iterator_range` utility with the standard `find_if` function.   
  
In the cases where the predicate was only used by `check_iterator_range` I just renamed the predicate's `apply()` method to `operator()` but when the predicate is used by other functions too I used lambda functions to pass `apply()` as a predicate operator.    
  
Note that `find_if` return `false` if the predicate fails in all members of the input range contrary to the implementation of `check_iterator_range` so now the predicate is returning the negation of the return value of the current implementation.

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 14:55:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813775097  

📁 include/boost/geometry/algorithms/detail/disjoint/multipoint_geometry.hpp

```diff
  82 |+                                       m_last,
  83 |+                                       point,
  84 |+                                       static_cast<base_type const&>(*this));
```

> Username: awulkiew  
> Created_at: 2021-11-23 14:55:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755206521  

You have changed the behavior of predicates and left the old names. E.g. in this case this predicate no longer is `unary_disjoint_predicate` it's `unary_intersects_predicate`.

> Username: barendgehrels  
> Created_at: 2021-12-01 09:15:04 UTC  
> Updated_at: 2022-02-02 10:54:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r759989084  

I would suggest to keep it as it was, `unary_disjoint_predicate` because the `not` is inconvenient and indeed it's something else. Same for predicates below.  
And then, additionally, a wrapping `unary_not_predicate` which takes this class and reverses the meaning, such that you can use it in the new way.

> Username: barendgehrels  
> Created_at: 2021-12-15 12:24:30 UTC  
> Updated_at: 2022-02-02 10:54:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r769577263  

This is marked as resolved, but not yet answered :-)


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 14:56:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813775643  

📁 include/boost/geometry/algorithms/detail/disjoint/multipoint_geometry.hpp

```diff
 107 | 
 108 |-         typedef unary_disjoint_predicate
 108 |+         using predicate_type = unary_disjoint_predicate
```

> Username: awulkiew  
> Created_at: 2021-11-23 14:56:20 UTC  
> Updated_at: 2021-11-23 14:56:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755207033  

So here we have `intersects_predicate`.


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 14:58:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813779130  

📁 include/boost/geometry/algorithms/detail/check_iterator_range.hpp

```diff
  39 |-             }
  40 |-         }
  41 |-         return AllowEmptyRange || first != beyond;
```

> Username: awulkiew  
> Created_at: 2021-11-23 14:58:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755209264  

After removing this and replacing with `find_if` we have to take care about this special condition because in some operations empty ranges are permitted and in other operations they're not.


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 14:59:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813781348  

📁 include/boost/geometry/algorithms/detail/disjoint/multipoint_geometry.hpp

```diff
 115 |+             boost::begin(multipoint2),
 116 |+             boost::end(multipoint2),
 117 |+             predicate_type(points1.begin(), points1.end()));
```

> Username: awulkiew  
> Created_at: 2021-11-23 14:59:19 UTC  
> Updated_at: 2021-11-23 14:59:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755210805  

Here we search for intersecting points and if they are not found then the result of disjoint is `true`. If multipoint is empty they are disjoint so ok.


---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 15:04:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813791236  

📁 include/boost/geometry/algorithms/detail/disjoint/multirange_geometry.hpp

```diff
  44 |     {
  45 |-         return BinaryPredicate::apply(query_geometry, m_geometry, m_strategy);
  45 |+         return ! BinaryPredicate::apply(query_geometry, m_geometry, m_strategy);
```

> Username: awulkiew  
> Created_at: 2021-11-23 15:04:51 UTC  
> Updated_at: 2021-11-23 15:05:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755217043  

Again this is now `intersects` predicate. Assuming that `BinaryPredicate` is `disjoint` predicate because technically this predicate is negating whatever `BinaryPredicate` is, but that's another issue. So the name should be changed as well.

> Username: awulkiew  
> Created_at: 2021-11-23 15:10:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755224523  

Regarding the names. Note that if we decided to name these predicate as `intersects` then we'd end up with confusing types, i.e. disjoint predicates passed into intersects predicates, e.g.:  
`unary_intersects_geometry_to_query_geometry` taking `disjoint_range_segment_or_box`. So I guess it'd be better to use name containing e.g. `unary_not`, `unary_not_disjoint` etc.


---

## Review 6 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 15:11:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813800521  

📁 include/boost/geometry/algorithms/detail/disjoint/linear_areal.hpp

```diff
 152 | 
 153 |-         typedef unary_disjoint_geometry_to_query_geometry
 153 |+         using unary_predicate_type = unary_disjoint_geometry_to_query_geometry
```

> Username: awulkiew  
> Created_at: 2021-11-23 15:11:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755225385  

This is now `intersects` or `not_disjoint`.


---

## Review 7 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 15:16:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813808151  

📁 include/boost/geometry/algorithms/detail/disjoint/linear_areal.hpp

```diff
 161 |+             std::find_if(boost::begin(interior_rings),
 162 |+                          boost::end(interior_rings), 
 163 |+                          unary_predicate_type(segment, strategy));
```

> Username: awulkiew  
> Created_at: 2021-11-23 15:16:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755231763  

Find if a segment intersects any interior ring. If not found and also for empty range return `true`. Ok.  
  
----  
  
`end` is created 2 times and makes the formatting harder. What do you think about replacing it with the code below?  
```  
auto const end = boost::end(interior_rings);  
return std::find_if(boost::begin(interior_rings), end,   
                    unary_predicate_type(segment, strategy)) == end;  
```


---

## Review 8 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 15:19:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813811113  

📁 include/boost/geometry/algorithms/detail/disjoint/multirange_geometry.hpp

```diff
  74 |+             std::find_if(boost::begin(multirange),
  75 |+                          boost::end(multirange), 
  76 |+                          unary_predicate_type(constant_size_geometry, strategy));
```

> Username: awulkiew  
> Created_at: 2021-11-23 15:19:02 UTC  
> Updated_at: 2021-11-23 15:19:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755233896  

Find if a point/segment/box intersects a geometry in a range and if not then return true. For empty range the result is true as well, ok.


---

## Review 9 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 15:23:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813817056  

📁 include/boost/geometry/algorithms/detail/distance/multipoint_to_geometry.hpp

```diff
 131 |+         if (boost::end(multipoint) == std::find_if(boost::begin(multipoint),
 132 |+                                                    boost::end(multipoint),
 133 |+                                                    predicate))
```

> Username: awulkiew  
> Created_at: 2021-11-23 15:23:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755238181  

For empty multipoint the result was `false` here before, now it would be `true`. There is no need to call whatever is below for empty geometry while calculating distance.


---

## Review 10 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 15:27:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813822495  

📁 include/boost/geometry/algorithms/detail/is_simple/linear.hpp

```diff
 294 |-                  <
 295 |-                      per_ls, // do not compute self-intersections
 296 |-                      true // allow empty multilinestring
```

> Username: awulkiew  
> Created_at: 2021-11-23 15:27:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755242286  

I think the comments should be preserved.


---

## Review 11 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 15:28:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813823513  

📁 include/boost/geometry/algorithms/detail/is_simple/linear.hpp

```diff
 291 |+         auto last = boost::end(multilinestring);
 292 |+ 
 293 |+         if ( ! (last == std::find_if(first, last, per_ls(strategy))))
```

> Username: awulkiew  
> Created_at: 2021-11-23 15:28:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755243053  

I'd write `auto const` and `!= last`.

> Username: awulkiew  
> Created_at: 2021-11-23 15:28:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755243825  

This will return `true` for empty multilinestring so ok.


---

## Review 12 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 15:30:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813826085  

📁 include/boost/geometry/algorithms/detail/is_valid/has_invalid_coordinate.hpp

```diff
  88 |         {
  88 |-             return ! point_has_invalid_coordinate::apply(point);
  89 |+             return point_has_invalid_coordinate::apply(point);
```

> Username: awulkiew  
> Created_at: 2021-11-23 15:30:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755245004  

This predicate is no longer `point_has_valid_coordinates`. I haven't checked but since there already is `point_has_invalid_coordinate` it's possible that this one is not needed.

> Username: vissarion  
> Created_at: 2021-11-24 10:48:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755918727  

I changed it to lambda and removed `point_has_valid_coordinates`


---

## Review 13 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 15:31:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813827921  

📁 include/boost/geometry/algorithms/detail/is_valid/has_invalid_coordinate.hpp

```diff
 100 |+             std::find_if(geometry::points_begin(geometry),
 101 |+                          geometry::points_end(geometry),
 102 |+                          point_has_valid_coordinates());
```

> Username: awulkiew  
> Created_at: 2021-11-23 15:31:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755246407  

This is a search for points with invalid coordinates.  
  
If not found then `true` is returned, also for empty range, ok.


---

## Review 14 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 15:34:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813831936  

📁 include/boost/geometry/algorithms/detail/is_valid/linear.hpp

```diff
 170 |+             boost::begin(multilinestring),
 171 |+             boost::end(multilinestring),
 172 |+             per_ls(visitor, strategy));
```

> Username: awulkiew  
> Created_at: 2021-11-23 15:34:24 UTC  
> Updated_at: 2021-11-23 15:34:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755249450  

The behavior is changed here. `false` was returned for empty range. But in this case this is probably irrelevant, right?

> Username: vissarion  
> Created_at: 2021-11-24 10:49:55 UTC  
> Updated_at: 2021-11-24 10:49:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755920812  

Yes, here is irrelevant.


---

## Review 15 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 15:37:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813836665  

📁 include/boost/geometry/algorithms/detail/is_valid/multipolygon.hpp

```diff
 269 |         {
 270 |-             return base::apply(polygon, m_policy, m_strategy);
 270 |+             return ! base::apply(polygon, m_policy, m_strategy);
```

> Username: awulkiew  
> Created_at: 2021-11-23 15:37:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755252873  

I'd probably also change the name from `per_polygon` to `is_invalid_polygon` or `is_polygon_invalid`.


---

## Review 16 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 15:39:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813839267  

📁 include/boost/geometry/algorithms/detail/is_valid/multipolygon.hpp

```diff
 295 |+             boost::begin(multipolygon),
 296 |+             boost::end(multipolygon),
 297 |+             per_polygon<VisitPolicy, Strategy>(visitor, strategy))))
```

> Username: awulkiew  
> Created_at: 2021-11-23 15:39:41 UTC  
> Updated_at: 2021-11-23 15:41:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755254871  

Is preserving the old behavior for empty multipolygons important here? Now it's different.  
  
After changing the name of `per_polygon` it'd be `find_if(..., is_invalid_polygon)` which is more clear.  
  
I'd think about calculating `end` once and replacing `!` and `==` with `!=`.


---

## Review 17 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 15:42:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813842568  

📁 include/boost/geometry/algorithms/detail/is_valid/polygon.hpp

```diff
  98 |         {
  99 |-             return detail::is_valid::is_valid_ring
  99 |+             return ! detail::is_valid::is_valid_ring
```

> Username: awulkiew  
> Created_at: 2021-11-23 15:42:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755257360  

Same story, the class defining this operator could be called `is_invalid_ring`.


---

## Review 18 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 15:43:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813843871  

📁 include/boost/geometry/algorithms/detail/is_valid/polygon.hpp

```diff
 115 |+             boost::begin(interior_rings),
 116 |+             boost::end(interior_rings),
 117 |+             per_ring<VisitPolicy, Strategy>(visitor, strategy));
```

> Username: awulkiew  
> Created_at: 2021-11-23 15:43:06 UTC  
> Updated_at: 2021-11-23 15:43:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755258444  

The behavior is the same. I'd only think about creating `end` once and replacing `per_ring` to `is_invalid_ring`.


---

## Review 19 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 15:46:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813848460  

📁 include/boost/geometry/algorithms/is_empty.hpp

```diff
  66 |+             boost::begin(interior_rings), 
  67 |+             boost::end(interior_rings),
  68 |+             []( auto const& range ){ return ! range_is_empty::apply(range); });
```

> Username: awulkiew  
> Created_at: 2021-11-23 15:46:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755261911  

This could probably be replaced with:  
```  
[]( auto const& range ){ return ! boost::empty(range); }  
```  
  
You could also check if `range_is_empty` is needed because it's possible that it's not used anywhere else.

> Username: vissarion  
> Created_at: 2021-11-24 11:00:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755933869  

> This could probably be replaced with:  
>   
> ```  
> []( auto const& range ){ return ! boost::empty(range); }  
> ```  
  
+1  
  
> You could also check if `range_is_empty` is needed because it's possible that it's not used anywhere else.  
  
It is used as default policy to `multi_is_empty`


---

## Review 20 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 15:48:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813851117  

📁 include/boost/geometry/policies/predicate_based_interrupt_policy.hpp

```diff
  44 |     inline bool apply(Range const& range)
  45 |     {
  46 |-         // if there is at least one unacceptable turn in the range, return false
```

> Username: awulkiew  
> Created_at: 2021-11-23 15:48:40 UTC  
> Updated_at: 2021-11-23 15:48:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755263894  

I'd probably keep the comment.


---

## Review 21 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 16:04:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813875333  

📁 include/boost/geometry/policies/predicate_based_interrupt_policy.hpp

```diff
  49 |+             []( auto const& turn ){ 
  50 |+                 return ! IsAcceptableTurnPredicate::apply(turn); }
  51 |+         ) || (AllowEmptyTurnRange && boost::empty(range)));
```

> Username: awulkiew  
> Created_at: 2021-11-23 16:04:02 UTC  
> Updated_at: 2021-11-24 11:07:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755279067  

This is quite complex. How about:  
```  
auto const end = boost::end(range);  
bool const has_unacceptable_turn  
    = std::find_if(boost::begin(range), end,  
                   []( auto const& turn ) {   
                       return ! IsAcceptableTurnPredicate::apply(turn);  
                   }) != end;  
has_intersections = ! has_unacceptable_turn  
                 && (! AllowEmptyTurnRange || ! boost::empty(range));  
```  
  
or something more clear like that.  
  
~Also the comparison operator wasn't correct, right? With `==` true would be returned if all turns were acceptable or for empty range. So `has_intersection` would be true for unacceptable turns and non-empty or `AllowEmptyTurnRange` and empty. Or am I missing something? Does all of the tests pass?~  
  
EDIT: Ok, I know what I missed, parenthesis. I altered the code above.

> Username: awulkiew  
> Created_at: 2021-11-24 11:09:30 UTC  
> Updated_at: 2021-11-24 11:10:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755945168  

There was one negation missing in my example. Maybe it's indeed better to keep these parenthesis and negate the whole expression as you did because then there is less negations. For that `has_unacceptable_turn` would have to be renamed to something like `has_acceptable_turns_or_empty`.

> Username: vissarion  
> Created_at: 2021-11-25 09:56:32 UTC  
> Updated_at: 2021-11-26 08:17:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r756735782  

I think there is one more wrong negation still in your proposed code. Indeed it is complicated. The correct should be  
```  
bool const has_unacceptable_turn  
    = std::find_if(boost::begin(range), end,  
                   []( auto const& turn ) {   
                       return ! IsAcceptableTurnPredicate::apply(turn);  
                   }) != end;  
has_intersections = has_unacceptable_turn  
    && !(AllowEmptyTurnRange && boost::empty(range));  
```  
with only one negation as in the original but less complicated as you propose.

> Username: awulkiew  
> Created_at: 2021-11-26 01:19:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r757176861  

Well, I don't know, the original comment states:  
```  
// if there is at least one unacceptable turn in the range, return false  
```  
So AFAIU if `has_unacceptable_turn` is `true` then `has_intersections` should be `false`, right? So either something is wrong with the naming or with `find_if`. Or I'm missing something.

> Username: vissarion  
> Created_at: 2021-11-26 12:37:27 UTC  
> Updated_at: 2021-11-26 12:37:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r757466988  

See the original code too, it seems that the original comment is wrong it should state   
```  
// if there is at least one unacceptable turn in the range, return true  
```

> Username: awulkiew  
> Created_at: 2021-11-26 13:05:55 UTC  
> Updated_at: 2021-11-26 13:05:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r757483981  

There are two booleans here:  
- `has_intersections` which is set to `true` if there are unacceptable turns or if the ring is empty when it shouldn't, this is used in `in_valid` and may have a different meaning than in other parts of the library, here it means that there the turns that were found are invalid [1].  
- return value which indicates if processing of turns should end [2], AFAIU `true` means stop [3]  
  
So if there are unacceptable turns found (or empty ring when it shouldn't) `has_intersections` should be `true` and this should also be the return value. So you're correct and I was missing that here `has_intersections` is used to return error condition.  
  
[1] https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/is_valid/has_valid_self_turns.hpp#L92-L95  
[2] https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/is_valid/has_valid_self_turns.hpp#L80-L83  
[3] https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/overlay/self_turn_points.hpp#L48-L59


---

## Review 22 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 16:04:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813875988  

📁 include/boost/geometry/policies/predicate_based_interrupt_policy.hpp

```diff
  85 |+             [&]( auto const& turn ){ 
  86 |+                 return ! m_predicate.apply(turn); }
  87 |+         ) || (AllowEmptyTurnRange && boost::empty(range)));
```

> Username: awulkiew  
> Created_at: 2021-11-23 16:04:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755279430  

See above.


---

## Review 23 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 16:23:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813904105  

📁 test/algorithms/test_is_valid.hpp

```diff
 108 |+             boost::end(interior_rings),
 109 |+             []( auto const& ring ){ return ! is_convertible_to_closed
 110 |+                 <decltype(ring)>::apply(ring); }
```

> Username: awulkiew  
> Created_at: 2021-11-23 16:23:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755301663  

`decltype(ring)` is const reference is this what you wanted?  
  
Also here different formatting of lambda expression is used.

> Username: vissarion  
> Created_at: 2021-11-25 10:17:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r756752743  

This is a mistake, should be `ring_type`


---

## Review 24 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-23 16:24:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-813905696  

📁 test/algorithms/test_is_valid.hpp

```diff
 131 |+             []( auto const& polygon ){ return ! is_convertible_to_closed
 132 |+                 <polygon_type>::apply(polygon); }
 133 |+         ) && !boost::empty(multi_polygon); // do not allow empty multi-polygon
```

> Username: awulkiew  
> Created_at: 2021-11-23 16:24:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r755302980  

Emptiness could be checked first because it's cheap.


---

## Comment 25

> Username: awulkiew  
> Created_at: 2021-11-23 16:26:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#issuecomment-976802981  

I think this is a good change. I've found some issues though.

---

## Comment 26

> Username: vissarion  
> Created_at: 2021-11-25 10:51:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#issuecomment-979088555  

Thanks for the review @awulkiew   
I addressed all your comments.

---

## Review 27 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-26 13:10:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-816770576  

📁 include/boost/geometry/algorithms/detail/disjoint/multirange_geometry.hpp

```diff
  73 |+         auto const end = boost::end(multirange);
  74 |+         return std::find_if(boost::begin(multirange), end, 
  75 |+             unary_predicate_type(constant_size_geometry, strategy)) == end;
```

> Username: awulkiew  
> Created_at: 2021-11-26 13:10:19 UTC  
> Updated_at: 2021-11-26 13:10:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r757486608  

I know that this is how this type was called before but don't you think that it'd be more readable if it instead of generic `predicate` we'd call it `not_disjoint`? Then this expression/sentence would be: find if not disjoint and the reader wouldn't have to check what the "predicate" is.


---

## Review 28 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-26 13:12:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-816772785  

📁 include/boost/geometry/algorithms/detail/is_simple/linear.hpp

```diff
 291 |+         // return true for empty multilinestring
 292 |+ 
 293 |+         using per_ls = per_linestring<Strategy>; // do not compute self-intersections
```

> Username: awulkiew  
> Created_at: 2021-11-26 13:12:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r757488183  

Here too, it's unclear what is checked below. I know that this is how it was implemented so I'd be ok with leaving it for now as it is but we could think about changing it.

> Username: vissarion  
> Created_at: 2021-11-26 13:36:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r757501800  

I agree, renamed it to `not_simple`.


---

## Review 29 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-26 13:15:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-816774751  

📁 include/boost/geometry/algorithms/detail/is_valid/has_invalid_coordinate.hpp

```diff
  91 |+                 geometry::points_begin(geometry), points_end,
  92 |+                 []( auto const& point ){ return point_has_invalid_coordinate::
  93 |+                     apply(point); }
```

> Username: awulkiew  
> Created_at: 2021-11-26 13:15:19 UTC  
> Updated_at: 2021-11-26 13:15:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r757489530  

Inconsistent formatting of lambdas:  
```  
[]( auto const& point ) {  
    return point_has_invalid_coordinate::apply(point);  
}  
```  
  
Or you could add `point_has_invalid_coordinate::operator()` and simply pass it here.

> Username: vissarion  
> Created_at: 2021-11-26 13:37:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r757502504  

Thanks, I changed the format.


---

## Review 30 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-26 13:17:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-816776362  

📁 include/boost/geometry/algorithms/detail/is_valid/multipolygon.hpp

```diff
 259 |     template <typename VisitPolicy, typename Strategy>
 260 |-     struct per_polygon
 260 |+     struct is_invalid_polygon
```

> Username: awulkiew  
> Created_at: 2021-11-26 13:17:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r757490690  

Other places could be changed like this. Now it's clear what's the purpose of this struct.


---

## Review 31 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-26 13:25:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-816783551  

📁 include/boost/geometry/policies/predicate_based_interrupt_policy.hpp

```diff
  52 |+                     return ! IsAcceptableTurnPredicate::apply(turn);
  53 |+                 }
  54 |+             ) != end;
```

> Username: awulkiew  
> Created_at: 2021-11-26 13:25:56 UTC  
> Updated_at: 2021-11-26 13:25:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r757495824  

I don't think that this formatting is correct.  
  
First of all we do accept up to 100 characters in a line so you don't have to put everything in subsequent lines.  
  
Another thing is that function name could be placed in the next line (maybe, do we allow this?):  
```  
bool const has_unacceptable_turn  
    = std::find_if(boost::begin(range), end,  
                   [](auto const& turn) {   
                       return ! IsAcceptableTurnPredicate::apply(turn);  
                   }) != end;  
```  
  
Another solution would be to create lambda expression before the call:  
```  
auto is_unacceptable = [](auto const& turn) { return ! IsAcceptableTurnPredicate::apply(turn); };  
```

> Username: vissarion  
> Created_at: 2021-11-26 13:44:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r757506729  

I think I prefer   
```  
bool const has_unacceptable_turn = std::find_if(boost::begin(range), end,  
    [](auto const& turn) {   
        return ! IsAcceptableTurnPredicate::apply(turn);  
    }) != end;  
```  
what do you think?

> Username: awulkiew  
> Created_at: 2021-11-26 13:47:25 UTC  
> Updated_at: 2021-11-26 13:47:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r757508793  

It looks good to me.


---

## Review 32 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-26 13:26:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-816783688  

📁 include/boost/geometry/policies/predicate_based_interrupt_policy.hpp

```diff
  93 |+                     return ! m_predicate.apply(turn);
  94 |+                 }
  95 |+             ) != end;
```

> Username: awulkiew  
> Created_at: 2021-11-26 13:26:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r757495927  

Same here.

> Username: vissarion  
> Created_at: 2021-11-26 13:51:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r757511257  

changed as above


---

## Review 33 [Commented]

> Username: awulkiew  
> Created_at: 2021-11-26 13:27:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-816784723  

📁 test/algorithms/test_is_valid.hpp

```diff
 110 |+                 return ! is_convertible_to_closed<ring_type>::apply(ring);
 111 |+             }
 112 |+         ) == end;
```

> Username: awulkiew  
> Created_at: 2021-11-26 13:27:19 UTC  
> Updated_at: 2021-11-26 13:27:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r757496668  

Is this style your personal preference or is this formatting caused by the 80 characters limit?

> Username: vissarion  
> Created_at: 2021-11-26 13:44:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r757507154  

personal preference but see in the comment above for an alternative

> Username: vissarion  
> Created_at: 2021-11-26 13:47:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r757508574  

what about   
```  
return std::find_if(boost::begin(interior_rings), end,   
    []( auto const& ring ){   
        return ! is_convertible_to_closed<ring_type>::apply(ring);  
    }) == end;  
```


---

## Comment 34

> Username: vissarion  
> Created_at: 2021-12-08 09:55:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#issuecomment-988662328  

:wave: @barendgehrels are you OK with merging this PR?

---

## Comment 35

> Username: awulkiew  
> Created_at: 2022-01-20 17:09:37 UTC  
> Updated_at: 2022-01-23 00:04:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#issuecomment-1017727089  

Since we're waiting for @barendgehrels with the approval I have a question/proposal.  
  
In the cases changed by PR the same pattern is used, i.e. `std::find_if(...) == end`. What do you think about replacing it with `std::none_of(...)`, which would be easier to understand?  
  
E.g. something like this:  
```  
auto const end = boost::end(multi);  
std::find_if(boost::begin(multi), end, covered_by_pred) == end;  
```  
could be replaced with:  
```  
std::none_of(boost::begin(multi), boost::end(multi), covered_by_pred);  
```

---

## Comment 36

> Username: vissarion  
> Created_at: 2022-01-24 15:39:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#issuecomment-1020232961  

> Since we're waiting for @barendgehrels with the approval I have a question/proposal.  
>   
> In the cases changed by PR the same pattern is used, i.e. `std::find_if(...) == end`. What do you think about replacing it with `std::none_of(...)`, which would be easier to understand?  
>   
> E.g. something like this:  
>   
> ```  
> auto const end = boost::end(multi);  
> std::find_if(boost::begin(multi), end, covered_by_pred) == end;  
> ```  
>   
> could be replaced with:  
>   
> ```  
> std::none_of(boost::begin(multi), boost::end(multi), covered_by_pred);  
> ```  
  
Thanks @awulkiew that's a very good idea, I have forgotten the existence of those (`std::none_of` and `std::any_of`). They are quite a fit for this case.

---

## Comment 37

> Username: barendgehrels  
> Created_at: 2022-02-02 10:53:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#issuecomment-1027812809  

> Since we're waiting for @barendgehrels with the approval I have a question/proposal.  
>   
> In the cases changed by PR the same pattern is used, i.e. `std::find_if(...) == end`. What do you think about replacing it with `std::none_of(...)`, which would be easier to understand?  
>   
> E.g. something like this:  
>   
> ```  
> auto const end = boost::end(multi);  
> std::find_if(boost::begin(multi), end, covered_by_pred) == end;  
> ```  
>   
> could be replaced with:  
>   
> ```  
> std::none_of(boost::begin(multi), boost::end(multi), covered_by_pred);  
> ```  
  
Yes, this is nice. I didn't approve yet because I had some questions, and they are not answered yet.  
  
My main question/issue was that there were many small structures containing `not_...` which, I think, would be more reusable if they were implemented in a positive way, and then a `not` around it.   
Anyway, that does not prevent merging, I'm fine with it.

---

## Review 38 [Approved]

> Username: barendgehrels  
> Created_at: 2022-02-02 10:54:23 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-819988166  

As just mentioned, you can decide to merge it like this or think if my comment makes sense to you.  
Thanks.

📁 include/boost/geometry/algorithms/detail/is_simple/linear.hpp

```diff
 272 |         {
 273 |-             return detail::is_simple::is_simple_linestring
 273 |+             return ! detail::is_simple::is_simple_linestring
```

> Username: barendgehrels  
> Created_at: 2021-12-15 12:25:49 UTC  
> Updated_at: 2022-02-02 10:54:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r769578212  

I like the change to `operator()`.  
But I would prefer to call this `is_simple` (or something with is), and avoid the not.  
Then (see comment below)

> Username: awulkiew  
> Created_at: 2022-02-02 11:34:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r797515959  

`is_not_simple`? ;)

> Username: vissarion  
> Created_at: 2022-02-02 11:39:01 UTC  
> Updated_at: 2022-02-02 11:39:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r797519275  

> `is_not_simple`? ;)  
  
why not.  
@barendgehrels what do you think ?

---

📁 include/boost/geometry/algorithms/detail/is_simple/linear.hpp

```diff
 291 |+         // return true for empty multilinestring
 292 |+ 
 293 |+         using not_simple = not_simple<Strategy>; // do not compute self-intersections
```

> Username: barendgehrels  
> Created_at: 2021-12-15 12:26:35 UTC  
> Updated_at: 2022-02-02 10:54:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r769578748  

Then I would call this `using not_simple = negate<not_simple<Strategy>>;`  
Same for all the other cases where you added `not`

> Username: awulkiew  
> Created_at: 2022-02-02 11:33:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r797514986  

This would be `std::unary_negate<is_simple<Strategy>>` and it'd have to be created with: `std::unary_negate<is_simple<Strategy>>(is_simple<Strategy>(strategy))` making it more complex than the function call below.

> Username: barendgehrels  
> Created_at: 2022-02-02 12:44:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r797567211  

Sure, that looks much better.  
  
And yes, in this case it looks more complex. But it is more flexible.

> Username: awulkiew  
> Created_at: 2022-02-02 13:03:45 UTC  
> Updated_at: 2022-02-02 13:03:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r797585883  

If we don't intend to use it anywhere else it doesn't have to be flexible. It makes no sense to add complexity only because we might some day in the future do something with it.

> Username: awulkiew  
> Created_at: 2022-02-02 13:18:50 UTC  
> Updated_at: 2022-02-02 13:18:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r797598054  

Besides is_simple + negate to represent is_not_simple is as flexible as is_not_simple + negate to represent is_simple. It is just a matter of preference, not flexibility.

> Username: awulkiew  
> Created_at: 2022-02-02 14:19:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r797652195  

It wouldn't be that complex if we used `std::not1`, i.e. `std::not1(is_simple<Strategy>(strategy))`, but this function is deprecated in C++17. In C++17 `std::not_fn` was introduced but we can't use that. So we'd be forced to write our own utility if we wanted this to look nice.


---

## Review 39 [Commented]

> Username: barendgehrels  
> Created_at: 2022-02-02 12:41:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-870483143  

📁 include/boost/geometry/algorithms/detail/is_simple/areal.hpp

```diff
 119 |+         return std::none_of(boost::begin(multipolygon), boost::end(multipolygon),
 120 |+                             [&](auto const& po) {
 121 |+                                 return ! detail::is_simple::is_simple_polygon(po, strategy);
```

> Username: barendgehrels  
> Created_at: 2022-02-02 12:41:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r797565309  

So this one looks good.


---

## Review 40 [Commented]

> Username: barendgehrels  
> Created_at: 2022-02-02 12:43:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-870484591  

📁 include/boost/geometry/algorithms/detail/is_simple/linear.hpp

```diff
 265 |     {
 266 |-         per_linestring(Strategy const& strategy)
 266 |+         not_simple(Strategy const& strategy)
```

> Username: barendgehrels  
> Created_at: 2022-02-02 12:43:15 UTC  
> Updated_at: 2022-02-02 12:43:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r797566312  

But this one should be: `is_simple`  
We shouldn't add many structures with `not_XXX`. They should be called `XXX` then they are reusable.


---

## Comment 41

> Username: barendgehrels  
> Created_at: 2022-02-07 09:54:16 UTC  
> Updated_at: 2022-02-07 09:54:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#issuecomment-1031273756  

Hi, since this PR is meant as an improvement, I'm still in favor of the `all_of` combined with a positive predicate (`all_of(... is_valid)`). It's as simple as it is now, but clearer. @vissarion what do you think?  
  
(So this is instead of one my earlier remarks (with the `find_if` / `negate` it is indeed more verbose, but that is not necessary)  
  
Sorry that this takes so long then, I think I raised this issue already in November (but can't find it now).   
Anyway, if you both think it's fine like this, I'm fine with merging or course.

---

## Comment 42

> Username: vissarion  
> Created_at: 2022-02-14 14:22:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#issuecomment-1039144132  

> Hi, since this PR is meant as an improvement, I'm still in favor of the `all_of` combined with a positive predicate (`all_of(... is_valid)`). It's as simple as it is now, but clearer. @vissarion what do you think?  
>   
> (So this is instead of one my earlier remarks (with the `find_if` / `negate` it is indeed more verbose, but that is not necessary)  
>   
> Sorry that this takes so long then, I think I raised this issue already in November (but can't find it now). Anyway, if you both think it's fine like this, I'm fine with merging or course.  
  
I agree. I changed all the double negations (combinations of `none_of` with negative predicates). If you are ok with the current version I will merge it. Thanks for your comments.

---

## Comment 43

> Username: awulkiew  
> Created_at: 2022-02-14 14:57:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#issuecomment-1039183963  

I'm ok with any approach as long as it doesn't require to make the code more complex. I also agree with @barendgehrels that it looks better this way.

---

## Review 44 [Approved]

> Username: barendgehrels  
> Created_at: 2022-02-14 18:32:33 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/934#pullrequestreview-870488162  

📁 include/boost/geometry/algorithms/detail/is_valid/linear.hpp

```diff
 144 |         {
 146 |-             return detail::is_valid::is_valid_linestring
 145 |+             return ! detail::is_valid::is_valid_linestring
```

> Username: barendgehrels  
> Created_at: 2022-02-02 12:46:35 UTC  
> Updated_at: 2022-02-14 18:32:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r797568960  

Here just a `!` is added - but it is less worse here, because `per_linestring` is not a generic name anyway, it means it is just locally applying something.

---

📁 include/boost/geometry/algorithms/detail/is_valid/linear.hpp

```diff
 169 |+         return std::none_of(boost::begin(multilinestring), 
 170 |+                             boost::end(multilinestring), 
 171 |+                             per_ls(visitor, strategy));
```

> Username: barendgehrels  
> Created_at: 2022-02-02 12:49:06 UTC  
> Updated_at: 2022-02-14 18:32:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r797570898  

So this means: none of them is not valid.  
Wouldn't it be much more clear if we state: `std::all_of(..., ..., is_valid)` ? So here: `std::all_of(..., ..., per_ls(visitor, strategy)`??


📁 include/boost/geometry/algorithms/detail/is_valid/multipolygon.hpp

```diff
 299 |-                            per_polygon<VisitPolicy, Strategy>(visitor, strategy)))
 294 |+         if (std::any_of(boost::begin(multipolygon), boost::end(multipolygon),
 295 |+                         is_invalid_polygon<VisitPolicy, Strategy>(visitor, strategy)))
```

> Username: barendgehrels  
> Created_at: 2022-02-02 12:50:30 UTC  
> Updated_at: 2022-02-14 18:32:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r797572011  

`if (! std::all_of(..., ..., is_valid)` would look much better


📁 include/boost/geometry/algorithms/detail/is_valid/polygon.hpp

```diff
 114 |+         return std::none_of(boost::begin(interior_rings), 
 115 |+                             boost::end(interior_rings),
 116 |+                             is_invalid_ring<VisitPolicy, Strategy>(visitor, strategy));
```

> Username: barendgehrels  
> Created_at: 2022-02-02 12:52:25 UTC  
> Updated_at: 2022-02-14 18:32:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#discussion_r797573404  

`return std::all_of(begin, end, is_valid_ring...)` would look much better.  
  
Etc etc, will not add comments for all of them individually.  
  
Sorry guys, double negations are usually not the best thing.


---

## Comment 45

> Username: barendgehrels  
> Created_at: 2022-02-14 18:34:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/934#issuecomment-1039420842  

> > Hi, since this PR is meant as an improvement, I'm still in favor of the `all_of` combined with a positive predicate (`all_of(... is_valid)`). It's as simple as it is now, but clearer. @vissarion what do you think?  
> > (So this is instead of one my earlier remarks (with the `find_if` / `negate` it is indeed more verbose, but that is not necessary)  
> > Sorry that this takes so long then, I think I raised this issue already in November (but can't find it now). Anyway, if you both think it's fine like this, I'm fine with merging or course.  
>   
> I agree. I changed all the double negations (combinations of `none_of` with negative predicates). If you are ok with the current version I will merge it. Thanks for your comments.  
  
Thank you!  
I'm OK now!

---
