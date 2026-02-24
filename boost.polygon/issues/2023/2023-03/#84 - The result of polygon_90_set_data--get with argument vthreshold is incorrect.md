# #84 - The result of polygon_90_set_data::get with argument vthreshold is incorrect [Open]

> Username: xshenf  
> Created at: 2023-03-21 03:45:22 UTC  
> Updated at: 2023-03-21 03:51:10 UTC  
> Url: https://github.com/boostorg/polygon/issues/84  

The code is as follows:  
``` C++  
#include "boost/polygon/gtl.hpp"  
#include <vector>  
#include <iostream>  
  
using namespace std;  
  
int main(int argc, char** argv)  
{  
    gtl::point_data<int> point[] = {{53, 17}, {53, 68}, {33, 68}, {33, 17}};  
    gtl::polygon_90_data<int> p90;  
    p90.set(point, point + 5);  
    gtl::rectangle_data<int> r1 = {10, 20, 30, 40};  
    gtl::rectangle_data<int> r2 = {10, 25, 35, 45};  
    gtl::rectangle_data<int> r3 = {10, 55, 43, 62};  
    gtl::polygon_90_set_data<int> ps1(gtl::VERTICAL), ps2(gtl::VERTICAL);  
    ps1.insert(r1);  
    ps1.insert(r3);  
    ps2.insert(r2);  
    ps2.insert(p90);  
  
    gtl::polygon_90_set_data<int> ps3 = ps1 | ps2;      
    vector<gtl::polygon_90_data<int>> p90s;  
    ps3.get(p90s, 8);  
    for (auto& p : p90s) {  
        for (auto& x : p) {  
          cout << "(" << x.x() << " " << x.y() << ")";  
        }  
        cout << endl;  
    }  
  
    return 0;  
}  
```  
The result is:  
```  
(17 33)(25 33)(25 30)(20 30)(20 10)(45 10)(17 10)  
(53 17)(33 17)(33 55)(10 55)(10 62)(33 62)(33 68)(53 68)  
```  
The input 17(point{53,17}) is the vertical coordinate, and the output 17(point{17,33}) is the horizontal coordinate.
