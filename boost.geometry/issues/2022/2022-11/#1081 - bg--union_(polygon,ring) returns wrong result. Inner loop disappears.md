# #1081 - bg::union_(polygon,ring) returns wrong result. Inner loop disappears. [Closed]

> Username: Mitsuhiko-Matsukawa  
> Created at: 2022-11-02 13:38:37 UTC  
> Updated at: 2022-11-23 13:20:44 UTC  
> Closed at: 2022-11-23 13:20:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/1081  

![image](https://user-images.githubusercontent.com/17040347/199502787-d78a1055-0193-4087-9f03-6d55dabc5e1e.png)  
Output1 and output2 should have same number of inner loops but output1 has two (correct) and output2 has one (wrong).  
  
boost1.80. Visual Studio 2017.  
```  
void Test()  
{  
    namespace bg = boost::geometry;  
    using point = bg::model::point<double, 2, bg::cs::cartesian>;  
    bg::model::polygon<point> polygon;  
    polygon.outer().assign({  
        {40,0},  
        {50,100},  
        {75,60},  
        {60,60},  
        {75,40},  
        {40,00}  
        });  
  
    polygon.inners().push_back({  
        {46.317137830014417,52.146832892889620},  
        {59.931267382602982,67.241906564861040},  
        {59.931267382602982,73.678978034969646},  
        {60,75},  
        {46.317137830014417,52.146832892889620}  
        });  
  
    bg::model::ring<point> ring{  
        {65,70},  
        {67,40},  
        {65,63},  
        {56.765190742466075,63.715020193369355},  
        {59.931267382602982,67.241906564861154},  
        {65,70}  
    };  
  
    bg::model::multi_polygon<bg::model::polygon<point>> output1;  
    bg::union_(polygon, ring, output1);  
  
    auto MovePoint = [](auto& point) {  
        point.set<1>(point.get<1>() + 100);  
    };  
    bg::for_each_point(polygon, MovePoint);  
    bg::for_each_point(ring, MovePoint);  
  
    bg::model::multi_polygon<bg::model::polygon<point>> output2;  
    bg::union_(polygon, ring, output2);  
  
    assert(output1.size() == 1 && output2.size() == 1 && output1[0].inners().size() == output2[0].inners().size());  
  
    return 0;  
}  
```

---

## Comment 1

> Username: Mitsuhiko-Matsukawa  
> Created at: 2022-11-15 16:33:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/1081#issuecomment-1315569908  

When turns make a cluster, sometimes inner loops disappear. I have changed equals stricter so that less clusters are made. This test code has passed but it might occur recession for existing issues.  
  
https://github.com/Mitsuhiko-Matsukawa/geometry/commit/952b0f658b793667dd5ffd5ec483e8ab227bfa0d  
  
It's better to fix cluster but it's hard for me.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2022-11-16 11:05:26 UTC  
> Updated at: 2022-11-16 11:06:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/1081#issuecomment-1316818081  

Thanks, I can reproduce it.  
  
This is my version:  
![image](https://user-images.githubusercontent.com/334849/202163868-011e741f-7c5c-468c-a66d-9b45a35d190a.png)  
  
And my (slightly adapted) code  
```  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/geometries.hpp>  
  
#include <fstream>  
#include <boost/geometry/io/svg/svg_mapper.hpp>  
  
template <typename Geometry1, typename Geometry2, typename Range>  
void create_svg(std::string const& filename, Geometry1 const& a, Geometry2 const& b, Range const& range)  
{  
    std::ofstream svg(filename.c_str());  
  
    using point_type = typename boost::geometry::point_type<Geometry1>::type;  
    boost::geometry::svg_mapper<point_type> mapper(svg, 400, 400);  
    mapper.add(a);  
    mapper.add(b);  
  
    mapper.map(a, "fill-opacity:0.5;fill:rgb(153,204,0);stroke:rgb(153,204,0);stroke-width:2");  
    mapper.map(b, "fill-opacity:0.3;fill:rgb(51,51,153);stroke:rgb(51,51,153);stroke-width:2");  
    for(auto const& g : range)  
    {  
        mapper.map(g, "opacity:0.8;fill:none;stroke:rgb(255,128,0);stroke-width:4;stroke-dasharray:1,7;stroke-linecap:round");  
    }  
}  
  
template <int Dim>  
bool test(double amount, bool first)  
{  
    namespace bg = boost::geometry;  
    using point = bg::model::point<double, 2, bg::cs::cartesian>;  
    bg::model::polygon<point> polygon;  
    polygon.outer().assign({  
        {40,0},  
        {50,100},  
        {75,60},  
        {60,60},  
        {75,40},  
        {40,00}  
        });  
  
    polygon.inners().push_back({  
        {46.317137830014417,52.146832892889620},  
        {59.931267382602982,67.241906564861040},  
        {59.931267382602982,73.678978034969646},  
        {60,75},  
        {46.317137830014417,52.146832892889620}  
        });  
  
    bg::model::ring<point> ring{  
        {65,70},  
        {67,40},  
        {65,63},  
        {56.765190742466075,63.715020193369355},  
        {59.931267382602982,67.241906564861154},  
        {65,70}  
    };  
  
    if (first)  
    {  
        bg::model::multi_polygon<bg::model::polygon<point>> output1;  
        bg::union_(polygon, ring, output1);  
  
        std::cout << "Output 1: " << bg::area(output1) << " " << output1.size() << " " << output1[0].inners().size() << std::endl;  
        create_svg("/tmp/case1.svg", polygon, ring, output1);  
    }  
  
    auto MovePoint = [&](auto& point) {  
        bg::set<Dim>(point, bg::get<Dim>(point) + amount);  
    };  
    bg::for_each_point(polygon, MovePoint);  
    bg::for_each_point(ring, MovePoint);  
  
    bg::model::multi_polygon<bg::model::polygon<point>> output2;  
    bg::union_(polygon, ring, output2);  
  
    bool wrong = output2.empty() || output2.front().inners().size() != 2;  
    if (first || wrong)  
    {  
        std::cout << "Output 2: " << bg::area(output2) << " " << output2.size() << " " << output2.front().inners().size() << " (" << amount << ")" << std::endl;  
        create_svg("/tmp/case2.svg", polygon, ring, output2);  
        if (wrong)  
        {  
            return false;  
        }  
    }  
    return true;  
}  
  
int main()  
{  
    bool first = true;  
    for (double a = 1.0; a < 20000; a += 1.0, first = false)  
    {  
        if (!test<1>(a, first))  
        {  
            break;  
        }  
    }  
}  
```  
  
Thanks for your suggestion as well. Appreciated! If I make the threshold `5` i/o `1000`, then it works and there are no regressions elsewhere.  
  
I agree it's better to "fix" cluster. But how... this code is relatively new, replacing the older (more complex) approach. It's a hard problem. I might look later again into it, but for now I propose to apply the changed threshold (PR will come)

---

## Comment 3

> Username: barendgehrels  
> Created at: 2022-11-16 13:11:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/1081#issuecomment-1316998590  

See PR, I made the threshold 3 now, necessary for one other run of tests.

---

## Comment 4

> Username: Mitsuhiko-Matsukawa  
> Created at: 2022-11-20 14:26:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/1081#issuecomment-1321146957  

@barendgehrels   
Thank you to adjust the threshold.  
Is it correct that if the threshold is 3, there are no regressions, and if the threshold is 1, there are regressions?  
  
I have another case that 3 makes wrong result and 1 makes correct result.  
![image](https://user-images.githubusercontent.com/17040347/202907676-3f54eadd-c3b3-4642-8ae3-421766322e4c.png)  
```  
void Test()  
{  
    namespace bg = boost::geometry;  
    using point = bg::model::point<double, 2, bg::cs::cartesian>;  
    bg::model::polygon<point> polygon;  
    polygon.outer().assign({  
        {411.083582272653245582,134.162757591540753310},  
        {421.316381618511854867,93.391951130757178134},  
        {414.749514308625293779,94.520554769539970152},  
        {414.403889713368130288,96.777762047893858721},  
        {306.396203695496922137,49.799635572622491964},  
        {411.083582272653245582,134.162757591540753310}  
        });  
  
    polygon.inners().push_back({  
        {409.910769975024663836,99.881422055531913884},  
        {412.848579034710724045,101.010025694314677480},  
        {408.182646998738732691,118.926608464425711986},  
        {409.910769975024663836,99.881422055531913884}  
        });  
  
    bg::model::ring<point> ring{  
        {423.217316892426424602,67.575142887590033070},  
        {414.403889713368130288,96.777762047893858721},  
        {410.429206867910465917,100.022497509887060119},  
        {412.848579034710780888,101.010025694314705902},  
        {412.502954439453560553,102.985082062381650303},  
        {416.996074177797027005,109.051326622514309861},  
        {427.364812035512670718,72.653859263295046844},  
        {423.217316892426424602,67.575142887590033070}  
    };  
  
    bg::model::multi_polygon<bg::model::polygon<point>> output;  
    bg::union_(polygon, ring, output);  
    assert(output.size() == 1 && output[0].inners().size() == 1);  
}  
```

---

## Comment 5

> Username: barendgehrels  
> Created at: 2022-11-23 08:22:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/1081#issuecomment-1324695422  

Thank you, I'll try this one as well together with the number of `1`

---

## Comment 6

> Username: barendgehrels  
> Created at: 2022-11-23 08:36:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/1081#issuecomment-1324707523  

> Thank you, I'll try this one as well together with the number of `1`  
  
With `1` there are indeed some (rare) regressions elsewhere. I will consider what to do.

---

## Comment 7

> Username: barendgehrels  
> Created at: 2022-11-23 10:42:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/1081#issuecomment-1324856787  

There is only one real regression. I'll go for `1` because it makes more sense than `3` and I will later try to fix the regression differently.
