# #1100 - Union of ring and ring returns wrong result. One of the input rings is disappeared. [Closed]

> Username: Mitsuhiko-Matsukawa  
> Created at: 2022-12-08 16:37:38 UTC  
> Updated at: 2023-04-08 11:57:51 UTC  
> Closed at: 2023-04-08 11:57:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/1100  

boost1.80.0 and 1.81.0(138d7fd)  
Visual Studio 2019. x64 debug  
![image](https://user-images.githubusercontent.com/17040347/206508462-0afae47d-c30e-44b5-96a1-c282a451e1cf.png)  
  
If I round the input coordinate, it works well.  
  
```  
void Test()  
{  
    namespace bg = boost::geometry;  
    using point = bg::model::point<double, 2, bg::cs::cartesian>;  
    using ring = bg::model::ring<point>;  
    using multi_polygon = bg::model::multi_polygon<bg::model::polygon<point>>;  
  
    // wrong case  
    ring input0 = {  
        {1.5300545419548890819e-16,2101},  
        {1,2101},  
        {1,2100},  
        {1.1102230246251565404e-16,2100},  
        {1.5300545419548890819e-16,2101}  
    };  
  
    ring input1 = {  
        {-0.19761611601674899941,2101},  
        {0,2100.6135231738085167},  
        {0,2100},  
        {-0.5,2100},  
        {-0.5,2101},  
        {-0.19761611601674899941,2101}  
    };  
  
    assert(bg::is_valid(input0));  
    assert(bg::is_valid(input1));  
    multi_polygon output;  
    bg::union_(input0, input1, output);  
    assert(bg::is_valid(output));  
  
    auto area_input0 = bg::area(input0);  
    auto area_input1 = bg::area(input1);  
    auto area_output = bg::area(output);  
    std::cout << "wrong case, " << area_input0 << ", " << area_input1 << ", " << area_output << std::endl;  
  
    // correct case  
    auto round = [](auto& point) {  
        static constexpr auto round = 1.0e6;  
        point.set<0>(std::round(point.get<0>() * round) / round);  
        point.set<1>(std::round(point.get<1>() * round) / round);  
    };  
    bg::for_each_point(input0, round);  
    bg::for_each_point(input1, round);  
  
    assert(bg::is_valid(input0));  
    assert(bg::is_valid(input1));  
    output.clear();  
    bg::union_(input0, input1, output);  
    assert(bg::is_valid(output));  
  
    area_input0 = bg::area(input0);  
    area_input1 = bg::area(input1);  
    area_output = bg::area(output);  
    std::cout << "correct case, " << area_input0 << ", " << area_input1 << ", " << area_output << std::endl;  
}  
```

---

## Comment 1

> Username: barendgehrels  
> Created at: 2022-12-14 09:23:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/1100#issuecomment-1350709358  

Thanks again for the test case and the clear reproduction scenario.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2023-03-15 10:35:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/1100#issuecomment-1469757136  

I can still reproduce it (testing it with the recent fixes)

---

## Comment 3

> Username: barendgehrels  
> Created at: 2023-04-08 11:57:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/1100#issuecomment-1500875757  

Fixed by PR #1128

---

## Comment 4

> Username: barendgehrels  
> Created at: 2023-04-08 11:57:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/1100#issuecomment-1500875915  

The issue itself is added as a unit test.
