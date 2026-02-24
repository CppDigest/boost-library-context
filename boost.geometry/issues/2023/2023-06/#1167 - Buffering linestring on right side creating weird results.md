# #1167 - Buffering linestring on right side creating weird results [Open]

> Username: bertrandpellenard  
> Created at: 2023-06-22 03:15:07 UTC  
> Updated at: 2023-06-25 13:43:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/1167  

Hello,  
I have a linestring and I am running the buffer function with an asymmetric distance. I want to buffer only on the right side of the linestring LINESTRING(0 0,-3 5,15 7,17 4,14 2)  
I am getting unexpected results.  
  
[  
![buffer_01](https://github.com/boostorg/boost/assets/10600487/a3a06aba-6162-4175-9df2-1fc37dae7486)  
![buffer_02](https://github.com/boostorg/boost/assets/10600487/7b8d5f5b-4c3b-48ad-8ab9-fc0e4b1c5268)  
](url)  
  
Here is an idea of the code:  
  
```  
using coordinate_type = double;  
  using point = boost::geometry::model::d2::point_xy<coordinate_type>;  
  using polygon = boost::geometry::model::polygon<point>;  
  
  // Declare strategies  
  const double buffer_distance = amount;  
  const int points_per_circle = 36;  
  boost::geometry::strategy::buffer::distance_symmetric<coordinate_type>  
      bg_both_sides_distance_strategy(buffer_distance);  
  
  boost::geometry::strategy::buffer::distance_asymmetric<coordinate_type>  
      bg_left_side_distance_strategy(buffer_distance, 0.0);  
  
  boost::geometry::strategy::buffer::distance_asymmetric<coordinate_type>  
      bg_right_side_distance_strategy(0.0, buffer_distance);  
  
  boost::geometry::strategy::buffer::join_miter bg_join_miter_strategy;  
  boost::geometry::strategy::buffer::join_round bg_join_round_strategy(  
      points_per_circle);  
  
  boost::geometry::strategy::buffer::end_round bg_end_round_strategy(  
      points_per_circle);  
  
  boost::geometry::strategy::buffer::end_flat bg_end_flat_strategy;  
  
  boost::geometry::strategy::buffer::point_circle bg_circle_strategy(  
      points_per_circle);  
  boost::geometry::strategy::buffer::side_straight bg_side_straight_strategy;  
  
 // Declare output  
  boost::geometry::model::multi_polygon<polygon> result;  
  
 // Declare/fill a linestring  
    boost::geometry::model::linestring<point> ls;  
    ls.reserve(pts_in.size());  
    for (const auto& pt : pts_in) {  
      ls.emplace_back(pt[0], pt[1]);  
    }  
  
boost::geometry::buffer(ls, result, bg_right_side_distance_strategy,  
                                  bg_side_straight_strategy,  
                                  bg_join_round_strategy, bg_end_round_strategy,  
                                  bg_circle_strategy);  
  
std::cout << "output: " << boost::geometry::wkt(result) << std::endl;  
```  
  
Thank you.  
Best regards.  
Bertrand

---

## Comment 1

> Username: barendgehrels  
> Created at: 2023-06-25 09:45:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/1167#issuecomment-1605994955  

Thanks for the report. I can reproduce it. Also with different coordinates - so it's not a precision issue.  
With a small left distance you see already some artefacts.  
  
I cannot look at it right now, but will do later.  
  
![image](https://github.com/boostorg/geometry/assets/334849/0430e96c-28b5-4172-ad2a-71aad5d81049)  
  
Adapted code:  
```  
    boost::geometry::strategy::buffer::distance_asymmetric<coordinate_type> bg_distance_strategy(0.025, 0.1);  
```  
  
and  
  
  
```  
    boost::geometry::model::linestring<point> ls;  
    boost::geometry::read_wkt("LINESTRING(0 0,-3 5,15 7,17 4,14 2)", ls);  
```

---

## Comment 2

> Username: bertrandpellenard  
> Created at: 2023-06-25 13:43:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/1167#issuecomment-1606098722  

I used `boost::geometry::strategy::buffer::distance_asymmetric<coordinate_type> bg_distance_strategy(0.0, 1.0);` in the pictures I shared
