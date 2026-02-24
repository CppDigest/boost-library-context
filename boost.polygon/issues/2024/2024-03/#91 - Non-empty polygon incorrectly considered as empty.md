# #91 - Non-empty polygon incorrectly considered as empty [Open]

> Username: corun1024  
> Created at: 2024-03-19 12:00:33 UTC  
> Updated at: 2024-04-22 13:25:02 UTC  
> Url: https://github.com/boostorg/polygon/issues/91  

Hiya,  
  
I've found an issue where some geometry that isn't empty is considered to be by boost polygon. Originally the geometry was part of a much more complex polygon, and small part of that polygon disappeared during a boolean operation which broke some of our code later down the line. I traced the issue and I've simplified it down to a single triangle:  
  
```  
std::vector<boost::polygon::point_data<int>> points{  
    {391663, 158218},  
    {339476, 267629},  
    {350305, 244924},  
    {391663, 158218}  
};  
  
boost::polygon::polygon_with_holes_data<int> polygon(points.begin(), points.end());  
  
boost::polygon::polygon_set_data<int> polygon_set;  
polygon_set.insert(polygon);  
  
std::cout << "Area (polygon data): " << boost::polygon::area(polygon) << "\n"; // Prints 47058  
std::cout << "Area (polygon set): " << boost::polygon::area(polygon_set) << "\n"; // Prints 0  
std::cout << "Empty: " << boost::polygon::empty(polygon_set) << "\n"; // Prints true  
std::cout << "Orient: " << boost::polygon::orientation(boost::polygon::segment_data<int>(points[0], points[1]), points[2]) << "\n"; // Print 1 (point is left of segment)  
```  
  
I believe this polygon to not be empty, however after being loaded into a polygon_set_data, the geometry disappears (shown by the area calculation), and for the same reason I assume, empty() returns true.  
  
I've included a call to orientation() in the sample code, which returns that the 3rd point is indeed to the left of the segment formed by the first two points, so the polygon is indeed correctly an anti-clockwise polygon, and also so that it's clear the points are not colinear.  
  
It is worth noting that if I move points[2] up by 2 units in the y axis, it causes the polygon to have the opposite winding. So it really is a very very thin polygon.  
  
Also worth noting that if I move points[2] down by 2 units in the y axis, the polygon doesn't disappear any more, the orientation() result is the same(as expected), and the above functions then return the correct results.  
  
Just wanted to add that at my work we have put probably billions of complex polygons through boost polygon, and this is the first serious issue we've encountered. It's a great library so kudos! :-)

---

## Comment 1

> Username: corun1024  
> Created at: 2024-04-22 13:25:01 UTC  
> Url: https://github.com/boostorg/polygon/issues/91#issuecomment-2069420665  

I happened to be reading about snap rounding, which is used by boost polygon, and I realised that it relates to this issue.  
  
With snap rounding, if a segment intersects a "hot pixel" (an integer square containing a segment endpoint, or an intersection), then the segment is "bent" such that passes through that hot pixel. This procedure allows the result to avoid containing any self intersections.  
  
In the example in the issue, one of the edges is bent due to snap rounding, causing the triangle collapse and become a quadrilateral with zero area. This then explains the above behaviour.  
  
This could be avoided using an improvement on the snap rounding algorithm called Stable Snap Rounding (https://www.sciencedirect.com/science/article/pii/S0925772112001551) however that would be a major undertaking for such a small problem on a project in maintenance mode :-). So, I think probably all that is needed would be a documentation improvement.  
  
It is surprising behaviour, worth documenting I think, that an input containing no intersections at all might return something other than the input. And indeed this also makes boost polygon operations not idempotent in the cases you might expect it to be.
