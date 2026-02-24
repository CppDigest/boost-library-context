# #1045 Geographic buffer line [Merged]

> Username: barendgehrels  
> Created at: 2022-07-27 13:04:12 UTC  
> Updated at: 2022-11-17 15:05:16 UTC  
> Merged at: 2022-08-03 09:19:11 UTC  
> Closed at: 2022-08-03 09:19:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/1045  

This is a fairly large PR adding two strategies for geographic buffers:  
* side_straight  
* join_round  
  
They are now tested and working for linestrings.  
Next phase will be: `end_round`  
After that I will test it with multi linestrings and polygons.  
  
It works pretty well in general, for example:  
![image](https://user-images.githubusercontent.com/334849/181253485-bf3f312a-2d2b-405f-bff5-a21c412c98b7.png)  
  
  
This picture is created with QGis and `.csv`, a next PR will add (optional) possibility to add `.csv` in testcode. Earlier I often used `.svg` but (especially for geographic), `.csv` or `.geojson` is quite convenient.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2022-07-27 13:08:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1045#pullrequestreview-1052474929  

📁 test/strategies/buffer_join_geo.cpp

```diff
  83 |+             out << id++ << ";3;" << bg::wkt(p) << std::endl;
  84 |+         }
  85 |+     }
```

> Username: barendgehrels  
> Created_at: 2022-07-27 13:08:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1045#discussion_r931041214  

This small `.csv` part is already there, and looks like:  
![image](https://user-images.githubusercontent.com/334849/181254623-e6c00558-495b-489b-9ee9-6dda9b4839f3.png)


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2022-07-28 14:18:53 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1045#pullrequestreview-1054123811  

Thanks, I am ok, only minor comments.

📁 include/boost/geometry/strategies/geographic/buffer_join_round.hpp

```diff
  53 |+     >
  54 |+     inline bool apply(Point const& ip, Point const& vertex,
  55 |+                 Point const& perp1, Point const& perp2,
```

> Username: vissarion  
> Created_at: 2022-07-28 13:46:22 UTC  
> Updated_at: 2022-07-28 14:18:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1045#discussion_r932240066  

something seems wrong with indentation here?

> Username: vissarion  
> Created_at: 2022-07-28 14:07:22 UTC  
> Updated_at: 2022-07-28 14:18:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1045#discussion_r932265044  

similar issues in `apply()` below


📁 include/boost/geometry/strategies/geographic/buffer_point_circle.hpp

```diff
 106 |+             calculation_type const eps = angle == 0 ? 1.0e-10 : 0.0;
 107 |+             auto const dir_rad = direct_t::apply(lon_rad, lat_rad,
 108 |+                                         buffer_distance, angle + eps,
```

> Username: vissarion  
> Created_at: 2022-07-28 14:01:22 UTC  
> Updated_at: 2022-07-28 14:18:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1045#discussion_r932258118  

I think is better to indent below `lon_rad` or use one tab

---

📁 include/boost/geometry/strategies/geographic/buffer_point_circle.hpp

```diff
 110 |-                 dir_r = direct_t::apply(get_as_radian<0>(point), get_as_radian<1>(point),
 111 |-                                         buffer_distance, angle,
 105 |+             // If angle is zero, shift angle a tiny bit to avoid spikes.
```

> Username: vissarion  
> Created_at: 2022-07-28 14:06:09 UTC  
> Updated_at: 2022-07-28 14:18:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1045#discussion_r932263620  

is this some fix on buffer point geographic strategy to avoid spikes or is another issue introduced by the new buffer line strategies?

> Username: barendgehrels  
> Created_at: 2022-08-03 07:10:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/1045#discussion_r936312328  

This was already the case in earlier code. It's now less but not yet 100% disappeared.  
We have to come back to it later. If I encounter it, I will create an issue.


📁 include/boost/geometry/strategies/geographic/buffer_side_straight.hpp

```diff
  57 |+     >
  58 |+     inline result_code apply(Point const& input_p1, Point const& input_p2,
  59 |+                 buffer_side_selector side,
```

> Username: vissarion  
> Created_at: 2022-07-28 14:09:56 UTC  
> Updated_at: 2022-07-28 14:18:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1045#discussion_r932267992  

should that be passed as `const&`?

> Username: barendgehrels  
> Created_at: 2022-08-03 07:10:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/1045#discussion_r936312542  

It is an enumerator. We normally don't do that.


📁 test/algorithms/buffer/test_buffer_geo.hpp

```diff
  70 |+         // too close, especially on artefacts in heavily curved input with flat ends.
  71 |+         // Therefore the default expectation can be modified. Inspect the SVG visually before doing this.
  72 |+         std::size_t too_close = 0, too_far = 0, total = 0;
```

> Username: vissarion  
> Created_at: 2022-07-28 14:15:28 UTC  
> Updated_at: 2022-07-28 14:18:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1045#discussion_r932274638  

we use one declaration per line style, right?

> Username: barendgehrels  
> Created_at: 2022-08-03 07:10:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1045#discussion_r936312794  

Indeed, thanks (and I applied also your other indentation comments)


---
