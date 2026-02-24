# #906 Fix buffer for macos-11, clang-12.0.5, x86-64 [Merged]

> Username: awulkiew  
> Created at: 2021-09-22 11:53:41 UTC  
> Updated at: 2021-09-28 22:35:03 UTC  
> Merged at: 2021-09-28 22:35:03 UTC  
> Closed at: 2021-09-28 22:35:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/906  

On macos-11 with clang-12.0.5 for this case:  
```  
cmls_t mls;  
bg::read_wkt("MULTILINESTRING((0 10,10 0),(10 0,0 0),(0 0,10 10))", mls);  
  
cmpo_t mpo;  
bg::buffer(mls, mpo,  
            bg::strategy::buffer::distance_symmetric<double>(100),  
            bg::strategy::buffer::side_straight(),  
            bg::strategy::buffer::join_miter(10),  
            bg::strategy::buffer::end_round(32),  
            bg::strategy::buffer::point_circle(32));  
```  
wrong buffer is generated, should be green, is blue (this is a regression):  
![obraz](https://user-images.githubusercontent.com/1226951/134337444-d4153616-bbfb-4a03-99e0-20a411bfcaba.png)  
  
On this platform slightly different pieces and turns are generated. They are close to these generated for msvc-14.1:  
![obraz](https://user-images.githubusercontent.com/1226951/134337926-92eec134-1908-4090-8512-12ae31349f4d.png)  
  
So with msvc-14.1 there are two clusters: (turns 3 and 4) and (turns 22 and 31). With macos-11+clang-12 the former cluster is not generated because turns 3 and 4 are marked as not traversable due to the check of the min distance which for these turns is around 8e-5 and the threshold is set to 1e-5.  
  
Btw, the comment states:  
```  
// This threshold is minimized to the point where fragile  
// unit tests of hard cases start to fail (5 in multi_polygon)  
// But it is acknowlegded that such a threshold depends on the  
// scale of the input.  
```  
if this is the case shouldn't this number depend on the size of the input instead of being an absolute value (probably on both geometry size and buffer distance)?

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2021-09-22 13:58:21 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/906#pullrequestreview-760953570  

📁 include/boost/geometry/algorithms/detail/buffer/turn_in_piece_visitor.hpp

```diff
 177 |             // scale of the input.
 178 |-             if (state.m_min_distance > 1.0e-5 || ! state.m_close_to_offset)
 178 |+             if (state.m_min_distance > 1.0e-4 || ! state.m_close_to_offset)
```

> Username: barendgehrels  
> Created_at: 2021-09-22 13:57:09 UTC  
> Updated_at: 2021-09-22 13:58:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/906#discussion_r713970688  

Thanks. I assume this needs a follow up too.

> Username: barendgehrels  
> Created_at: 2021-09-22 13:58:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/906#discussion_r713972339  

_(I mean: from my side)_


📁 test/algorithms/buffer/buffer_multi_linestring.cpp

```diff
 215 |+     {
 216 |+         // This issue was detected for CCW order and only CW is tested by default.
 217 |+         typedef bg::model::polygon<P, ! Clockwise> polygon_ccw;
```

> Username: barendgehrels  
> Created_at: 2021-09-22 13:57:22 UTC  
> Updated_at: 2021-09-22 13:58:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/906#discussion_r713970897  

`using` ?

> Username: awulkiew  
> Created_at: 2021-09-28 22:25:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/906#discussion_r718009454  

done

---

📁 test/algorithms/buffer/buffer_multi_linestring.cpp

```diff
 214 | 
 215 |+     {
 216 |+         // This issue was detected for CCW order and only CW is tested by default.
```

> Username: barendgehrels  
> Created_at: 2021-09-22 13:57:59 UTC  
> Updated_at: 2021-09-22 13:58:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/906#discussion_r713971534  

Right... this is actually not the intention, but with this comment it's fine for now.


---
