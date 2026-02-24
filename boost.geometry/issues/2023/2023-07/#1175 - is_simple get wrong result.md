# #1175 - is_simple get wrong result. [Closed]

> Username: fililili  
> Created at: 2023-07-11 02:50:49 UTC  
> Updated at: 2023-07-23 02:28:24 UTC  
> Closed at: 2023-07-23 02:28:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/1175  

In OGC, the simple's definition looks like below:  
![image](https://github.com/boostorg/geometry/assets/74575700/e08b9529-a161-4fe9-8deb-9390ede28497)  
but boost geometry judge such linestring  
![image](https://github.com/boostorg/geometry/assets/74575700/1a27e557-0e8c-4a23-a413-5c62bc248c01)  
 as simple, I think it's a bug.  
 Here is the code ([godbolt link](https://godbolt.org/z/Gzzq5E31E) ).  
```cpp  
#include <iostream>  
#include <boost/geometry.hpp>  
  
namespace bg = boost::geometry;  
  
int main()  
{  
    typedef bg::model::linestring<bg::model::d2::point_xy<double>> linestring_type;  
  
    linestring_type line;  
    bg::read_wkt("LINESTRING (0 0, 0 2, 1 2, -1 0)", line);  
  
    bool is_valid = bg::is_valid(line);  
  
    if (is_valid) {  
        std::cout << "The line is valid." << std::endl;  
    } else {  
        std::cout << "The line is invalid." << std::endl;  
    }  
  
    bool is_simple = bg::is_simple(line);  
  
    if (is_simple) {  
        std::cout << "The line is simple." << std::endl;  
    } else {  
        std::cout << "The line is simple." << std::endl;  
    }  
  
    bool is_empty = bg::is_empty(line);  
    if (is_empty) {  
        std::cout << "The line is empty." << std::endl;  
        return 0;  
    }  
  
    bool is_closed = bg::equals(line.front(), line.back());  
    if (is_closed) {  
        std::cout << "The line is closed." << std::endl;  
    } else {  
        std::cout << "The line is not closed." << std::endl;  
    }  
  
    linestring_type line2;  
    bg::read_wkt("LINESTRING (0 0, 0 1, 0 2, 1 2, 0 1, -1 0)", line2);  
  
    bool is_valid2 = bg::is_valid(line2);  
  
    if (is_valid2) {  
        std::cout << "The line2 is valid." << std::endl;  
    } else {  
        std::cout << "The line2 is invalid." << std::endl;  
    }  
  
    bool is_simple2 = bg::is_simple(line2);  
  
    if (is_simple2) {  
        std::cout << "The line2 is simple." << std::endl;  
    } else {  
        std::cout << "The line2 is simple." << std::endl;  
    }  
  
    bool is_empty2 = bg::is_empty(line2);  
    if (is_empty2) {  
        std::cout << "The line2 is empty." << std::endl;  
        return 0;  
    }  
  
    bool is_closed2 = bg::equals(line2.front(), line2.back());  
    if (is_closed2) {  
        std::cout << "The line2 is closed." << std::endl;  
    } else {  
        std::cout << "The line2 is not closed." << std::endl;  
    }  
  
    bool is_equal = bg::equals(line, line2);  
    if (is_equal) {  
        std::cout << "line and line2 are equal." << std::endl;  
    } else {  
        std::cout << "line and line2 are not equal." << std::endl;  
    }  
    return 0;  
}  
```

---

## Comment 1

> Username: awulkiew  
> Created at: 2023-07-22 23:04:41 UTC  
> Updated at: 2023-07-22 23:05:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/1175#issuecomment-1646687810  

`is_simple` returns `false` for me in both cases (VS2022/msvc-14.3, debug/1.83-beta1).  
You print incorrect message for non-simple lines. May this be the issue?
