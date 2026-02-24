# #981 [fix] sectionalize with const geometry [Merged]

> Username: barendgehrels  
> Created at: 2022-02-23 13:55:36 UTC  
> Updated at: 2022-05-22 07:31:25 UTC  
> Merged at: 2022-03-02 08:34:40 UTC  
> Closed at: 2022-03-02 08:34:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/981  

Fixes #980  
  
For this algorithm, I use the `helper_geometry` in the test only, because the box-type should be writable anyway (it writes section boxes) and we can conveniently use that point type

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2022-02-23 13:56:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/981#pullrequestreview-891115364  

📁 include/boost/geometry/geometries/point_xy.hpp

```diff
  50 | public:
  51 |-     /// \constructor_default_no_init
  51 |+     /// Constructor_default_no_init
```

> Username: barendgehrels  
> Created_at: 2022-02-23 13:56:25 UTC  
> Updated_at: 2022-02-23 13:56:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/981#discussion_r812915930  

Unrelated

> Username: awulkiew  
> Created_at: 2022-02-23 14:04:03 UTC  
> Updated_at: 2022-02-23 14:04:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/981#discussion_r812923274  

This was doxygen alias I think:  
https://github.com/boostorg/geometry/blob/develop/doc/doxy/Doxyfile#L93

> Username: barendgehrels  
> Created_at: 2022-02-23 14:05:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/981#discussion_r812925026  

> This was doxygen alias I think: https://github.com/boostorg/geometry/blob/develop/doc/doxy/Doxyfile#L93  
  
Sorry. Thanks, it's fixed.


---

## Review 2 [Changes requested]

> Username: awulkiew  
> Created_at: 2022-02-23 14:14:13 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/981#pullrequestreview-891130523  

📁 include/boost/geometry/algorithms/detail/sections/sectionalize.hpp

```diff
 955 |         >::apply(geometry, robust_policy, sections,
 956 |                  strategy,
 957 |                  ring_id, max_count);
```

> Username: awulkiew  
> Created_at: 2022-02-23 14:08:16 UTC  
> Updated_at: 2022-02-23 14:14:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/981#discussion_r812927291  

If `sections` are passed as function argument then you don't have to pass `point_type` as template parameter. `box_type` should rather be taken from the type of `sections` inside `apply()`. The number of template parameters should be minimal that is required for the dispatching to work. `point_type` is not needed for that. It's an additional information needed only in `apply()`.

> Username: barendgehrels  
> Created_at: 2022-02-23 14:55:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/981#discussion_r812978778  

Yes, it’s true. I started in another way, which is why I added them.  
But you are right, I can remove them now. I’ll do that. Thanks.

> Username: barendgehrels  
> Created_at: 2022-02-23 15:04:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/981#discussion_r812988223  

The other way was actually: pass the point all the way, even calling sectionalize with that.  
But we don’t need to use it like that. Just this box point-type is perfect. I’ll update it later today.

---

📁 include/boost/geometry/algorithms/detail/sections/sectionalize.hpp

```diff
 633 |             <
 634 |-                 closure<Polygon>::value, Reverse, point_type, DimensionVector
 634 |+                 closure<Polygon>::value, Reverse, Point, DimensionVector
```

> Username: awulkiew  
> Created_at: 2022-02-23 14:13:57 UTC  
> Updated_at: 2022-02-23 14:14:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/981#discussion_r812932949  

So here instead of passing `Point` into `secionalize_range` you could remove this template parameter and instead inside `secionalize_range::apply()` take it from `Sections` as:  
```  
typename geometry::point_type<typename Sections::box_type>::type  
```

> Username: barendgehrels  
> Created_at: 2022-02-23 17:49:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/981#discussion_r813157988  

:heavy_check_mark: done  
  
I moved it to in `sectionalize_part` which makes it now simpler than it was before.  
  
(though the diff might be now larger because I changed some `typedef` with `using`)  
  
Thanks for your comment.


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2022-02-23 17:50:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/981#pullrequestreview-891453339  

📁 include/boost/geometry/algorithms/detail/sections/sectionalize.hpp

```diff
 383 |- template
 384 |- <
 385 |-     typename Point,
```

> Username: barendgehrels  
> Created_at: 2022-02-23 17:50:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/981#discussion_r813158633  

So this `Point` is gone.


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2022-02-23 17:51:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/981#pullrequestreview-891454649  

📁 include/boost/geometry/algorithms/detail/sections/sectionalize.hpp

```diff
 644 |-         for (typename detail::interior_iterator<Polygon const>::type
 645 |-                 it = boost::begin(rings); it != boost::end(rings); ++it, ++ring_id.ring_index)
 637 |+         auto const& rings = interior_rings(poly);
```

> Username: barendgehrels  
> Created_at: 2022-02-23 17:51:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/981#discussion_r813159602  

I think this is right, but I'm not 100% sure.   
Should it stay `interior_return_type` ?

> Username: awulkiew  
> Created_at: 2022-02-23 19:33:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/981#discussion_r813248333  

`const&` is fine because even if `interior_rings()` returned proxy reference like `reference_wrapper` this object would live as long as the const reference `rings`.  
  
You could also use `auto&&` which would be able to represent both true references and proxy references exactly as they were returned from `interior_rings()`. But since we don't need perfect forwarding here enforcing constness is probably better.


---

## Review 5 [Approved]

> Username: vissarion  
> Created_at: 2022-02-24 15:09:17 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/981#pullrequestreview-892572903  

LGTM thanks

📁 include/boost/geometry/algorithms/detail/sections/sectionalize.hpp

```diff
 165 |     template <typename Segment>
 166 |     static inline void apply(Segment const& seg,
 167 |                 int directions[Count])
```

> Username: vissarion  
> Created_at: 2022-02-24 15:07:01 UTC  
> Updated_at: 2022-02-24 15:09:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/981#discussion_r813971865  

this is unrelated but you can also fix the indentation here.

> Username: barendgehrels  
> Created_at: 2022-03-02 08:34:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/981#discussion_r817457874  

It's actually not really wrong: we started with this. And then later aligned under the bracket. Which is (IMO) also inconvenient (because if you rename the method, it's wrong again). There is no perfect way. I will leave it for now.

> Username: awulkiew  
> Created_at: 2022-03-02 09:26:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/981#discussion_r817501051  

Shouldn't there be only 1-tab indentation then?  
  
> because if you rename the method, it's wrong again  
  
Maybe clang-format could be a solution for this.

> Username: barendgehrels  
> Created_at: 2022-03-02 12:36:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/981#discussion_r817650100  

Yes, 1-tab it should have been I think.  
  
`clang-format` is for 90% nice indeed. But the last 10% is awful, from all what I've seen, and it destroys readability which we carefully wrote everywhere (nearly).  
  
I'm quite sensible to code layout and we try to do it carefully with optimum readability. Such as this one  
  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/srs/projections/impl/dms_parser.hpp#L249-L257  
  
If you handle this with clang-format, it's destroyed. The same for streaming:  
  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/simplify.hpp#L133-L151  
  
This is also destroyed by clang-format.  
  
But clang-format has many options, and I'm not an expert. If you have a configuration which works well in all cases, we can consider that.


---
