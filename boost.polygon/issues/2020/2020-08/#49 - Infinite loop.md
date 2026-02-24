# #49 - Infinite loop [Open]

> Username: lshamis  
> Created at: 2020-08-06 17:43:39 UTC  
> Updated at: 2021-03-21 18:25:03 UTC  
> Url: https://github.com/boostorg/polygon/issues/49  

A call to `construct_voronoi` blocks forever in the following code:  
```cpp  
int main() {  
  using poly_segment = boost::polygon::segment_data<double>;  
  std::vector<poly_segment> segments;  
  
  std::ifstream file("bad_voronoi.segments.txt");  
  while (!file.fail()) {  
    double x1, y1, x2, y2;  
    file >> x1 >> y1 >> x2 >> y2;  
    segments.push_back({{x1, y1}, {x2, y2}});  
  }  
  
  // // Doesn't help:  
  // std::vector<poly_segment> split_segments;  
  // boost::polygon::intersect_segments(split_segments, segments.begin(), segments.end());  
  // segments = std::move(split_segments);  
  
  boost::polygon::voronoi_diagram<double> vd;  
  boost::polygon::construct_voronoi(segments.begin(), segments.end(), &vd);  
}  
```  
[bad_voronoi.segments.txt](https://github.com/boostorg/polygon/files/5036881/bad_voronoi.segments.txt)  
  
I've tracked the issue down to the `_build()` method in `voronoi_diagram.hpp`, specifically in the loop that remove degenerate vertices:  
```cpp  
          do {  
            e->vertex0(v);  
            e = e->rot_next();  
          } while (e != v->incident_edge());  
```  
  
An odd property: removing *any* segment from `bad_voronoi.segments.txt` will unbreak the infinite loop. You need all 1912 segments to trigger the fault.  
  
![bad_voronoi](https://user-images.githubusercontent.com/3943815/89563472-56f09c00-d7d0-11ea-9754-a0879c76d00b.png)

---

## Comment 1

> Username: eadf  
> Created at: 2021-03-21 18:25:03 UTC  
> Url: https://github.com/boostorg/polygon/issues/49#issuecomment-803636796  

Not only does you data contain floats, it also contains 6 intersections (data interpreted as int and/or float)  
```  
intersection @(-2950.0, -16950.0) lines:[(-3000.0, -16950.0, 13000.0, -16950.0), (-2950.0, -17000.0, -2950.0, -3495.21643474)]  
intersection @(-2611.955764954004, 4000.0) lines:[(-2612.64822957, 4107.12305833, -2610.38580768, 3757.13037063), (-2650.0, 4000.0, -311.90771173, 4000.0)]  
intersection @(788.1152702477493, 4000.0) lines:[(-311.90771173, 4000.0, 2320.0, 4000.0), (787.927142963, 4029.10296017, 789.219955469, 3829.10713862)]  
intersection @(-994.242279910348, 9000.0) lines:[(-994.356608226, 9017.68638938, -994.033405099, 8967.687434), (-1994.26317262, 9000.0, -794.238101369, 9000.0)]  
intersection @(1055.8005501413174, 9000.0) lines:[(-794.238101369, 9000.0, 1105.80159477, 9000.0), (1055.60056262, 9030.93771758, 1055.92376575, 8980.93876219)]  
intersection @(1855.4940544237638, 9050.0) lines:[(1805.49300979, 9050.0, 3105.52017031, 9050.0), (1855.26064568, 9086.10792299, 1855.5838488, 9036.1089676)]  
```
