# #1052 [test] add optional csv output to view buffer test cases in qgis [Merged]

> Username: barendgehrels  
> Created at: 2022-08-10 12:57:43 UTC  
> Updated at: 2024-12-14 11:14:53 UTC  
> Merged at: 2022-09-09 07:54:56 UTC  
> Closed at: 2022-09-09 07:54:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1052  



---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2022-09-01 09:06:54 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1052#pullrequestreview-1092989370  

LGTM, thanks!

📁 test/algorithms/buffer/debug_buffer_info.hpp

```diff
   9 |+ 
  10 |+ 
  11 |+ #ifndef BOOST_GEOMETRY_TEST_BUFFER_DEBUG_BUFFER_INFO_HPP
```

> Username: vissarion  
> Created_at: 2022-09-01 08:34:19 UTC  
> Updated_at: 2022-09-01 09:06:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1052#discussion_r960372735  

Should we include the whole path here? i.e. `BOOST_GEOMETRY_TEST_ALGORITHMS_BUFFER_DEBUG_BUFFER_INFO_HPP`

> Username: barendgehrels  
> Created_at: 2022-09-09 07:31:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1052#discussion_r966720499  

Sure, thanks. :heavy_check_mark:


📁 test/algorithms/buffer/test_buffer_csv.hpp

```diff
  61 |+ 
  62 |+     template <typename Turns>
  63 |+     inline void write_turns(Turns const &turns)
```

> Username: vissarion  
> Created_at: 2022-09-01 09:02:13 UTC  
> Updated_at: 2022-09-01 09:06:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1052#discussion_r960401864  

We write it `Turns const& turns`

> Username: barendgehrels  
> Created_at: 2022-09-09 07:32:20 UTC  
> Updated_at: 2022-09-09 07:32:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1052#discussion_r966721197  

:heavy_check_mark:

---

📁 test/algorithms/buffer/test_buffer_csv.hpp

```diff
  31 |+ 
  32 |+     template <typename PieceCollection>
  33 |+     inline void apply(PieceCollection const &collection, int phase)
```

> Username: vissarion  
> Created_at: 2022-09-01 09:02:51 UTC  
> Updated_at: 2022-09-01 09:06:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1052#discussion_r960402468  

We write it `PieceCollection const& collection`

---

📁 test/algorithms/buffer/test_buffer_csv.hpp

```diff
  85 |+ 
  86 |+     template <typename Pieces, typename OffsettedRings>
  87 |+     inline void write_pieces(Pieces const &pieces, OffsettedRings const &offsetted_rings)
```

> Username: vissarion  
> Created_at: 2022-09-01 09:04:00 UTC  
> Updated_at: 2022-09-01 09:06:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1052#discussion_r960403641  

as above we use `const& varaible_name`

---

📁 test/algorithms/buffer/test_buffer_csv.hpp

```diff
  97 |+         for (const auto &piece : pieces)
  98 |+         {
  99 |+             bg::segment_identifier seg_id = piece.first_seg_id;
```

> Username: vissarion  
> Created_at: 2022-09-01 09:06:16 UTC  
> Updated_at: 2022-09-01 09:06:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1052#discussion_r960405847  

should this be `const` ?

> Username: barendgehrels  
> Created_at: 2022-09-09 07:34:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/1052#discussion_r966722660  

Sure, thanks. Updated it and your other comments.


---
