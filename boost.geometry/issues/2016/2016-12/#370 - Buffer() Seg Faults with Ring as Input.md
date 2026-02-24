# #370 - Buffer() Seg Faults with Ring as Input [Closed]

> Username: xmrflipflop  
> Created at: 2016-12-01 03:55:11 UTC  
> Updated at: 2017-04-04 14:48:52 UTC  
> Closed at: 2017-03-26 10:52:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/370  

On boost version 1.58.0 and C++14, boost geometry buffer with strategies fails when the input geometry given is a ring. Changing the type to a polygon behaves as expected.  
  
I don't have newer boost versions available for testing unfortunately.  
  
Test code below.  
```  
        std::cout << "Using Boost "  
                  << BOOST_VERSION / 100000     << "."  // major version  
                  << BOOST_VERSION / 100 % 1000 << "."  // minor version  
                  << BOOST_VERSION % 100                // patch level  
                  << std::endl;  
  
        // Set up boost geometry types  
        namespace bg = boost::geometry;  
        using point_type = bg::model::point<float, 2, bg::cs::cartesian>;  
        using polygon_type = bg::model::polygon<point_type>;  
        using ring_type = bg::model::ring<point_type>;  
  
        // Declare strategies  
        constexpr float buffer_distance = 0.5F;  
        const int points_per_circle = 36;  
        bg::strategy::buffer::distance_symmetric<float> distance_strategy(buffer_distance);  
        bg::strategy::buffer::join_round join_strategy(points_per_circle);  
        bg::strategy::buffer::end_round end_strategy(points_per_circle);  
        bg::strategy::buffer::point_circle circle_strategy(points_per_circle);  
        bg::strategy::buffer::side_straight side_strategy;  
  
        bg::model::multi_polygon<polygon_type> bufferedPolygon;  
        ring_type originalPolygon;  
//        polygon_type originalPolygon;  
  
        bg::append(originalPolygon, point_type { 14.4980001F, 10.5F });  
        bg::append(originalPolygon, point_type { 14.4980001F, 12.420001F });  
        bg::append(originalPolygon, point_type { 15.9980001F, 12.420001F });  
        bg::append(originalPolygon, point_type { 15.9980001F, 10.5F });  
        bg::append(originalPolygon, point_type { 14.4980001F, 10.5F });  
  
        bg::correct(originalPolygon);  
  
        bg::buffer(originalPolygon, bufferedPolygon, distance_strategy, side_strategy,  
            join_strategy, end_strategy, circle_strategy);  
```

---

## Comment 1

> Username: xmrflipflop  
> Created at: 2016-12-01 04:10:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/370#issuecomment-264074080  

Can confirm same behaviour with C++11 and current ``geometry-develop`` branch.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2016-12-01 18:04:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/370#issuecomment-264247009  

I can reproduce this. Will look at it next Wednesday.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2017-03-26 10:52:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/370#issuecomment-289272381  

Sorry, it was not next Wednesday, but (hopefully) still in time to make it for 1.64.  
It is now fixed by https://github.com/boostorg/geometry/commit/b320ddf8e67f1e4fcd30bbd9651906cc35ef27ed
