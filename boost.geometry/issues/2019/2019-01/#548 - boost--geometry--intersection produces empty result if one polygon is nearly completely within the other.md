# #548 - boost::geometry::intersection produces empty result if one polygon is nearly completely within the other [Closed]

> Username: doeblerh  
> Created at: 2019-01-22 08:58:08 UTC  
> Updated at: 2019-11-06 11:01:48 UTC  
> Closed at: 2019-11-06 11:01:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/548  

Consider the following two polygons:  
![bgbug](https://user-images.githubusercontent.com/21227649/51521355-b07bba80-1e26-11e9-8acc-52a046f23d3c.png)  
  
Clearly, b1 is mostly within b2, thus their intersection is not empty. However, `boost::geometry:intersection` produces an empty result:  
```c++  
#include <deque>  
#include <iostream>  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
  
namespace bg = boost::geometry;  
  
typedef bg::model::d2::point_xy<double> point;  
typedef bg::model::polygon<point> polygon;  
  
int  
main (int argc, char** argv)  
{  
  std::deque<polygon> output;  
  
  double yOffset = 0.0;  
  if (argc > 1)  
    yOffset = std::stod (argv[1]);  
  polygon b1 {{  
               point (143526.96086588613, 113763.64549686472 + yOffset),  
               point (76161.913300625194, 96673.360975963747 + yOffset),  
               point (76161.913300625194, 71504.239234902911 + yOffset),  
               point (145207.72299290277, 81831.630077016118 + yOffset),  
               point (143526.96086588613, 113763.64549686472 + yOffset)  
    }};  
  bg::correct(b1);  
  polygon b2 {{  
               point (-47053.678255174891, -657486.29482312244),  
               point (-610948.08772840793, 300004.45446108561),  
               point (-132202.71308630385, 581951.65919770207),  
               point (431691.69638692908, -375539.09008650586),  
               point (-47053.678255174891, -657486.29482312244)  
    }};  
  bg::correct(b2);  
  
  bg::intersection (b1, b2, output);  
  std::cout << "intersection (b1, b2): " << output.size() << std::endl;  
  
  bool wi = bg::within(b1, b2);  
  std::cout << "within (b1, b2):" << wi << std::endl;  
  
  return 0;  
}  
```  
Output:  
```  
intersection (b1, b2): 0  
within (b1, b2):0  
```  
This seems to happen only because the corner of `b1` nearly touches an egde of `b2`. Frankly, shifting `b1` vertically by +1 or -1 (can be done with `yOffset`/`argv[1]`) resolves this:  
`./a.out 1` output:  
```  
intersection (b1, b2): 1  
within (b1, b2):0  
```  
`./a.out -1` output:  
```  
intersection (b1, b2): 1  
within (b1, b2):1  
```  
  
Not sure, whether this is related to #511.  
  
Boost version: 1.69, but also tested in 292e3df03eb2e9720bfaf1f3ebfe4bb0b309923c and e23b0778707a045c33c569670ba69510f3365baa, same results there.  
Compiler: `g++ (GCC) 8.2.1 20181127`  
System: Arch linux 64 bit

---

## Comment 1

> Username: barendgehrels  
> Created at: 2019-03-02 16:54:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/548#issuecomment-468938037  

The same as for issue #555 : the issue if you define **BOOST_GEOMETRY_NO_ROBUSTNESS**. Then internal rescaling is turned off, and the operation is successful. I added this case also to the testsuite.  
  
Of course - I have to add that all your code will then be called without rescaling. But this will probably be OK.  
  
Rescaling will be turned off by default, hopefully in one or two versions from now on (so not yet the coming version).  
  
Therefore effectively it is solved already. Is it OK if I close this ticket?

---

## Comment 2

> Username: barendgehrels  
> Created at: 2019-11-06 11:01:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/548#issuecomment-550259989  

Closing issue (no answer since March 2 - and fixed by avoiding rescaling, which will be the default in 1.73 or later)
