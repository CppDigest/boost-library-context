# #1383 - union_ of two edge-adjacent rings is empty [Closed]

> Username: MaartenHilferink  
> Created at: 2025-04-07 23:21:55 UTC  
> Updated at: 2025-04-19 17:56:18 UTC  
> Closed at: 2025-04-08 19:02:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/1383  

When unioning polygons with double coordinates of Dutch CBS Buurten and municipal maps, I often miss resulting areas.   
  
I had first documented cases in: https://github.com/ObjectVision/GeoDMS/issues/882  
  
I've finally isolated a case of unioning 2 outer rings s1 with 5 different points and s2 with 7 different points, with 3 common points (C, D, E) indicating 2 touching segments, of which segment (C, D) is only 15.625 [mm]  
  
code:  
```  
#include "boost/geometry.hpp"  
#include "boost/geometry/algorithms/union.hpp"  
  
namespace bg = boost::geometry;  
  
constexpr bool ClockWise = true;  
constexpr bool Closed    = true; // each ring is closed, i.e. last point == first point  
  
using Point        = bg::model::point<double, 2, bg::cs::cartesian>;  
using Ring         = bg::model::ring   <Point, ClockWise, Closed>;  
using Polygon      = bg::model::polygon<Point, ClockWise, Closed>;  
using MultiPolygon = bg::model::multi_polygon<Polygon>;  
  
int main(int argc, char** argv)  
{  
	Ring s1{  
		{ 173904.25160630842, 604340     }, // A  
		{ 173803.203125     , 604351.875 }, // B  
		{ 173802.59375      , 604351.9375}, // C  
		{ 173802.578125     , 604351.9375}, // D  
		{ 173797.5588235294 , 604360     }, // E  
		{ 174000            , 604360     }, // F  
		{ 174000            , 604340     }, // G  
		{ 173904.25160630842, 604340     }  // A  
	};  
	std::cout << "area of s1 = " << bg::area(s1) << std::endl;  
  
	Ring s2{  
		{ 173797.5588235294 , 604360     }, // E  
		{ 173802.578125     , 604351.9375}, // D  
		{ 173802.59375      , 604351.9375}, // C  
		{ 173794.765625     , 604352.875 }, // H  
		{ 173735.58701923076, 604360     }, // I  
		{ 173797.5588235294 , 604360     }  // E  
	};  
	std::cout << "area of s2 = " << bg::area(s2) << std::endl;  
        // spoiler alert: note that C is 15.625 [mm] east of D and E->D intersects C->H very close to D.  
  
	MultiPolygon result;  
	bg::union_(s1, s2, result);  
  
	if (result.empty())  
		std::cout << "result of union(s1, s2) is empty" << std::endl;  
	else for (auto r: result)  
	{  
		std::cout << "outer" << std::endl;  
		for (auto r : r.outer())  
		{  
			std::cout << bg::get<0>(r) << " " << bg::get<1>(r) << std::endl;  
		}  
	}  
}  
```  
  
output:  
  
![Image](https://github.com/user-attachments/assets/1434c4eb-80dd-4bb2-96ce-6ec62dfc4483)  
  
  
I am using MSVC 17.13.3 x64 and boost::geometry 1_86 through a recent baseline of vcpkg

---

## Comment 1

> Username: MaartenHilferink  
> Created at: 2025-04-07 23:48:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/1383#issuecomment-2784877402  

![Image](https://github.com/user-attachments/assets/2edae94e-24cf-4328-8ddd-601583f8b4fd)

---

## Comment 2

> Username: MaartenHilferink  
> Created at: 2025-04-08 00:27:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/1383#issuecomment-2784925189  

Stepping into boost::geometry::union_:  
  
![Image](https://github.com/user-attachments/assets/9e1c963c-2e23-4993-8fa6-a0185783de80)  
  
I see a suspicious extra "method_crosses" turn [2] between [1](touching D) and [3] (touching E)  
  
![Image](https://github.com/user-attachments/assets/9b75d217-0cd3-4a1f-9059-18f6fafb13ff)

---

## Comment 3

> Username: MaartenHilferink  
> Created at: 2025-04-08 16:20:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/1383#issuecomment-2786997765  

get_turn_info on (C, D) x (C, H, I) is handled as cross:  
  
![Image](https://github.com/user-attachments/assets/7efa7991-d0e2-47ef-8eb4-a9da051743fa)

---

## Comment 4

> Username: MaartenHilferink  
> Created at: 2025-04-08 16:43:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/1383#issuecomment-2787067639  

![Image](https://github.com/user-attachments/assets/2859f074-bdc1-427f-bf37-8f1c47a883c3)

---

## Comment 5

> Username: MaartenHilferink  
> Created at: 2025-04-08 17:00:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/1383#issuecomment-2787110140  

E->D does indeed cross C-> H, so s2: (E, D, C, H, I, E) self intersects although it shares the two edges E->D and D->C with s1: (A, B, C, D, E, F, G, A)

---

## Comment 6

> Username: MaartenHilferink  
> Created at: 2025-04-08 19:02:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/1383#issuecomment-2787410426  

Fixed by calling the following code when `boost::geometry::is_valid(ring)` returns false:  
  
```  
template<typename Geometry>  
bg_multi_polygon_t clean_geometry_with_buffer0(Geometry&& input)  
{  
	namespace bg = boost::geometry;  
  
	bg_multi_polygon_t output;  
	bg::strategy::buffer::join_round join_strategy(0);  
	bg::strategy::buffer::end_round end_strategy(0);  
	bg::strategy::buffer::distance_symmetric<double> distance_strategy(0);  
	bg::strategy::buffer::side_straight side_strategy;  
	bg::strategy::buffer::point_circle circle_strategy(0);  
  
	bg::buffer(input, output,  
		distance_strategy, side_strategy,  
		join_strategy, end_strategy, circle_strategy);  
  
	return output;  
}  
  
```

---

## Comment 7

> Username: barendgehrels  
> Created at: 2025-04-19 17:56:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/1383#issuecomment-2816804968  

hi @MaartenHilferink !   
  
Thanks for reporting the issue - and for debugging it and finding the problem!
