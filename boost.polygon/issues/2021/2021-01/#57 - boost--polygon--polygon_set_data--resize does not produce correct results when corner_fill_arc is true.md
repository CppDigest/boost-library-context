# #57 - boost::polygon::polygon_set_data::resize does not produce correct results when corner_fill_arc is true [Open]

> Username: by408  
> Created at: 2021-01-28 19:09:18 UTC  
> Updated at: 2021-01-29 01:38:49 UTC  
> Url: https://github.com/boostorg/polygon/issues/57  

When corner_fill_arc is true resize returns results that are incorrect on long edges. It needs to bias the long edges by exact resizing amount, and round around corners. In current implementation, long edges are not biased exactly. This is due to using a discrete circle in minkowski_offset. Minkowski  convolution needs to be done with a continuous circle, if you discretize the circle you make errors in the bias. The results of resize with corner_fill_arc  must be similar to boost::geometry::buffer function.  
  
The resize with or without corner_fill_arc  must return same results on long edges, only difference needs to be near corners.  
If you run the following code you get:  
max coordinates without rounding: 110  110  
max coordinates with rounding: 109.239  109.239  
  
ALSO: If you create a square that is 1000x1000, the output is empty.  
Calling with numbers bigger than 8 does not produce any results: Like    
polygon_set1.resize(10.0, true, 9);  // output is empty  
polygon_set1.resize(10.0, true, 36); // output is empty  
   
```  
void testBoostResize(){  
   using polygon = boost::polygon::polygon_with_holes_data<double>;  
   using point = boost::polygon::polygon_traits<polygon>::point_type;  
   std::vector<point> pts;  
   pts.push_back(point(0,0));  
   pts.push_back(point(100,0));  
   pts.push_back(point(100,100));  
   pts.push_back(point(0,100));  
   polygon pol;  
   boost::polygon::set_points(pol, pts.begin(), pts.end());  
  
   boost::polygon::polygon_set_data<double>  polygon_set2;  
   polygon_set2.insert(pol);  
   polygon_set2.resize(10.0); // resize without rounding  
   vector<polygon> resized2;  
   polygon_set2.get(resized2);  
   double max_x = -1000;  
   double max_y = -1000;  
    for (auto it = resized2[0].begin(); it != resized2[0].end(); it++){  
        const auto &pt = *it;  
        max_x = std::max(max_x, pt.x());  
        max_y = std::max(max_y, pt.y());  
   }  
   cout <<"max coordinates without rounding: "<< max_x <<"  "<< max_y<<endl;  
  
  
   boost::polygon::polygon_set_data<double>  polygon_set1;  
   polygon_set1.insert(pol);  
   polygon_set1.resize(10.0, true, 8); // resize with rounding  
   vector<polygon> resized1;  
   polygon_set1.get(resized1);  
   max_x = -1000;  
   max_y = -1000;  
    for (auto it = resized1[0].begin(); it != resized1[0].end(); it++){  
        const auto &pt = *it;  
        max_x = std::max(max_x, pt.x());  
        max_y = std::max(max_y, pt.y());  
   }  
   cout <<"max coordinates with rounding: "<< max_x <<"  "<< max_y<<endl;  
}  
```
