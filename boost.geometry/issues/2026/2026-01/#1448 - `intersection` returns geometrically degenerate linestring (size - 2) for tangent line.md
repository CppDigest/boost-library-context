# #1448 - `intersection` returns geometrically degenerate linestring (size < 2) for tangent line [Open]

> Username: nilsnolde  
> Created at: 2026-01-28 17:48:13 UTC  
> Updated at: 2026-01-28 17:52:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/1448  

With boost 1.89, consider the following program where we compute the intersection of a box and a tangent line:  
  
<details>  
<summary> code </summary>  
  
```c++    
  #include <boost/geometry.hpp>                                                                                                                                                                                     
  #include <boost/geometry/geometries/point_xy.hpp>                                                                                                                                                                 
  #include <boost/geometry/geometries/linestring.hpp>                                                                                                                                                               
  #include <boost/geometry/geometries/box.hpp>                                                                                                                                                                      
  #include <boost/geometry/geometries/multi_linestring.hpp>                                                                                                                                                         
  #include <iostream>                                                                                                                                                                                               
                                                                                                                                                                                                                    
  namespace bg = boost::geometry;                                                                                                                                                                                   
                                                                                                                                                                                                                    
  int main() {                                                                                                                                                                                                      
      using point_t = bg::model::d2::point_xy<int32_t>;                                                                                                                                                                 
      using linestring_t = bg::model::linestring<point_t>;                                                                                                                                                          
      using box_t = bg::model::box<point_t>;                                                                                                                                                                        
      using multi_linestring_t = bg::model::multi_linestring<linestring_t>;                                                                                                                                         
                                                                                                                                                                                                                    
      box_t clip_box{{0, 0}, {4096, 4096}};                                                                                                                                                                         
                                                                                                                                                                                                                    
      // Line with one point exactly on the box border (4096, 4000)                                                                                                                                                 
      // and the other point outside the box (4100, 4010)                                                                                                                                                           
      linestring_t line{{4100, 4000}, {4100, 4010}};                                                                                                                                                                
                                                                                                                                                                                                                    
      multi_linestring_t result;                                                                                                                                                                                    
      bg::intersection(line, clip_box, result);                                                                                                                                                                     
                                                                                                                                                                                                                    
      std::cout << "Input line: " << bg::wkt(line) << "\n";                                                                                                                                                         
      std::cout << "Clip box: " << bg::wkt(clip_box) << "\n";                                                                                                                                                       
      std::cout << "Result size: " << result.size() << "\n";                                                                                                                                                        
                                                                                                                                                                                                                    
      for (size_t i = 0; i < result.size(); ++i) {                                                                                                                                                                  
          std::cout << "Result[" << i << "]: " << bg::wkt(result[i])                                                                                                                                                
                    << " (num_points=" << result[i].size() << ")\n";                                                                                                                                                
      }                                                                                                                                                                                                                           
                                                                                                                                                                                                                    
      return 0;                                                                                                                                                                                                     
  }                                                                                                                                                                                                        
```  
</details>  
  
Compile with `g++ -std=c++17 -o boost_intersection_bug boost_intersection_bug.cpp` and run `./boost_intersection_bug`.  
  
The output would be  
  
```  
  Input line: LINESTRING(4096 4000,4100 4010)                                                                                                                                                                       
  Clip box: POLYGON((0 0,0 4096,4096 4096,4096 0,0 0))               
  Result size: 1                                                                                                                                                                                                    
  Result[0]: LINESTRING(4096 4000) (num_points=1)  
```  
  
I'd have hoped for `Result size: 0` because there is actually no intersection ~~for all practical purposes~~ well, that's bit far.. I understand that it's _geometrically_ correct that the intersection is a point, but type-wise it's more a bug IMHO to return an invalid linestring type.  
  
Sorry if this is a duplicate, skimming the issues didn't surface anything like-wise.
