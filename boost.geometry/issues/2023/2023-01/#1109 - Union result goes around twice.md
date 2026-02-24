# #1109 - Union result goes around twice. [Closed]

> Username: Mitsuhiko-Matsukawa  
> Created at: 2023-01-24 16:07:57 UTC  
> Updated at: 2023-03-07 15:23:36 UTC  
> Closed at: 2023-03-07 15:23:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/1109  

boost1.81.0  
Visual Studio 2019. x64 debug  
  
![image](https://user-images.githubusercontent.com/17040347/214344848-5d9942b5-3d86-421a-9687-8dc1397fa3ef.png)  
  
```  
void Test()  
{  
    namespace bg = boost::geometry;  
    using point = bg::model::point<double, 2, bg::cs::cartesian>;  
    using ring = bg::model::ring<point>;  
    using multi_polygon = bg::model::multi_polygon<bg::model::polygon<point>>;  
    multi_polygon input0;  
    ring input1;  
  
    constexpr double ng_y = -115.4;  
    //constexpr double ng_y = -115.5;  
  
    input0.resize(2);  
    // green  
    input0[0].outer().assign({  
        {-63,-88},  
        {0,ng_y},  
        {0,-124},  
        {-63,-124},  
        {-63,-88}  
    });  
    // blue  
    input0[1].outer().assign({  
        {0,-150},  
        {0,-124},  
        {13,-124},  
        {13,-121},  
        {0,ng_y},  
        {0,-88},  
        {40,-88},  
        {40,-150},  
        {0,-150}  
    });  
    // red  
    input1.assign({  
        {0,-88},  
        {0,ng_y},  
        {-10,-88},  
        {0,-88}  
    });  
  
    assert(bg::is_valid(input0));  
    assert(bg::is_valid(input1));  
    multi_polygon output;  
    bg::union_(input0, input1, output);  
  
    for (size_t i = 0; i < output.size(); ++i) {  
        std::cout << "====polygon " << i << std::endl;  
        for (auto& point : output[i].outer()) {  
            std::cout << point.get<0>() << "," << point.get<1>() << std::endl;  
        }  
    }  
  
    auto area_in = bg::area(input0) + bg::area(input1);  
    auto area_out = bg::area(output);  
    assert(area_in >= area_out);  
}  
```  
result  
```  
====polygon 0  
0,-115.4  
0,-124  
-63,-124  
-63,-88  
-4.35207e-13,-115.4  
0,-115.4  
0,-124  
-63,-124  
-63,-88  
0,-115.4  
====polygon 1  
0,-115.4  
-10,-88  
0,-88  
40,-88  
40,-150  
0,-150  
0,-124  
13,-124  
13,-121  
-4.35207e-13,-115.4  
0,-115.4  
0,-88  
40,-88  
40,-150  
0,-150  
0,-124  
13,-124  
13,-121  
0,-115.4  
```  
  
If ng_y is not -111.4 but -111.5, it works well.

---

## Comment 1

> Username: barendgehrels  
> Created at: 2023-01-25 09:44:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/1109#issuecomment-1403340532  

Thanks for the report. I can reproduce it on Linux / clang

---

## Comment 2

> Username: barendgehrels  
> Created at: 2023-01-25 14:48:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/1109#issuecomment-1403736662  

Related to #1108

---

## Comment 3

> Username: barendgehrels  
> Created at: 2023-02-15 15:07:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/1109#issuecomment-1431517386  

Fixed by mentioned PR
