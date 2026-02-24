# #1320 [test] Add test case for geographic intersection for polygons touching North Pole [Merged]

> Username: vissarion  
> Created at: 2024-10-02 12:53:42 UTC  
> Updated at: 2024-11-27 09:02:20 UTC  
> Merged at: 2024-10-03 09:14:08 UTC  
> Closed at: 2024-10-03 09:14:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1320  

Adding test case from issue https://github.com/boostorg/geometry/issues/480

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-10-02 13:35:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1320#pullrequestreview-2342979984  

📁 test/algorithms/intersects/intersects_sph_geo.cpp

```diff
 104 |+   boost::geometry::intersection(bigTile,usa,out2);
 105 |+   BOOST_CHECK(out1.size() == 1);
 106 |+   BOOST_CHECK(boost::geometry::area(out1) == boost::geometry::area(out2));
```

> Username: barendgehrels  
> Created_at: 2024-10-02 13:35:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/1320#discussion_r1784542749  

Shouldn't we use a floating-point check with epsilon?

> Username: vissarion  
> Created_at: 2024-10-03 09:06:43 UTC  
> Updated_at: 2024-10-03 09:06:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1320#discussion_r1785886469  

That should be OK without epsilon because the two computed polygons are exactly the same and thus their areas.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2024-10-02 13:35:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1320#pullrequestreview-2342982331  

📁 test/algorithms/intersects/intersects_sph_geo.cpp

```diff
  92 |+ BOOST_AUTO_TEST_CASE(usaIntersectBigTile90) {
  93 |+   auto bigTileWKt     = "POLYGON((-182.25 -2.25,-182.25 85,-90 85,4.5 85,4.5 -2.25,-90 -2.25,-182.25 -2.25))";
  94 |+   auto bigTileWKtOn90 = "POLYGON((-182.25 -2.25,-182.25 90,-90 90,4.5 90,4.5 -2.25,-90 -2.25,-182.25 -2.25))";
```

> Username: barendgehrels  
> Created_at: 2024-10-02 13:35:57 UTC  
> Updated_at: 2024-10-02 13:35:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1320#discussion_r1784543717  

I think we never use this camelCase convention...  
Also (minor), can be const

> Username: vissarion  
> Created_at: 2024-10-03 09:13:23 UTC  
> Updated_at: 2024-10-03 09:13:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/1320#discussion_r1785895002  

Thanks you are right, I was just copy-pasting here from the original issue. Fixed now.


---

## Review 3 [Approved]

> Username: barendgehrels  
> Created_at: 2024-10-02 13:36:30 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1320#pullrequestreview-2342984427  

Minor comments but looks good to me

---
