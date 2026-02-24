# #868 - polygon/ring self-intersection correct [Open]

> Username: kleunen  
> Created at: 2021-06-14 17:35:02 UTC  
> Updated at: 2025-07-19 13:51:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/868  

I have developed a polygon correct approach (remove self-intersection, correct order, correct inners), based on boost geometry:  
https://github.com/kleunen/boost_geometry_correct  
  
The approach for calculating and removing self-intersection is vastly simpler and more efficient than existing libraries.   
It was developed for tilemaker:  
https://github.com/systemed/tilemaker  
  
But it might be useful for including in boost geometry as well

---

## Comment 1

> Username: awulkiew  
> Created at: 2021-06-15 09:07:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-861327591  

Hi and thanks for this proposal!  
  
I think that a function removing self-intersections should be a part of the library.  
  
AFAIK @barendgehrels was working on this and the result was `dissolve()` algorithm:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/extensions/algorithms/dissolve.hpp  
Have you compared the two in terms of the quality of the result (whatever this is) and performance?  
If the results were different, would both of them be useful in different scenarios or would only one be needed?  
  
In Boost.Geometry we divide algorithms into two parts, coordinate-system agnostic and specific/dependent. The former we call algorithms and the latter strategies. So strategies define some coordinate-system specific building blocks like calculating a side of a point wrt a line or performing an intersection of two segments, etc. Then the coordinate-system agnostic algorithm uses these building blocks.  
Can your solution be adapted to work in non-cartesian coordinate system or is it cartesian-only?  
Does your algorithm fit into this algorithm-strategy model and if yes what are the coordinate-system specific building blocks?  
  
I'm not sure if naming it `correct()` is a good idea. This function is for adapting the data to the compile-time parameters of a type, i.e. point order and closure. We probably shouldn't change its behavior at this point because some of the users might unexpectedly notice a drop of performance. But let's leave the naming for later.

---

## Comment 2

> Username: kleunen  
> Created at: 2021-06-15 09:36:20 UTC  
> Updated at: 2021-06-15 10:04:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-861348046  

> AFAIK @barendgehrels was working on this and the result was `dissolve()` algorithm:  
> https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/extensions/algorithms/dissolve.hpp  
> Have you compared the two in terms of the quality of the result (whatever this is) and performance?  
> If the results were different, would both of them be useful in different scenarios or would only one be needed?  
  
We tried (from the tilemaker project), but we never got this extension to compile. You can see on the github I generate the examples from @barendgehrels:   
https://github.com/kleunen/boost_geometry_correct  
  
TBH i do not understand @barendgehrels approach, so I can not judge on how this compares. He had issues on certain polygons, and this is a generalized approach. Only one approach would make sense.   
  
There is also a discussion going on about this method on JTS:  
https://github.com/locationtech/jts/issues/652#issuecomment-861264198  
  
GEOS also have done an extensive comparison on polygon repair methods:  
https://docs.google.com/document/d/19YEQS0goSpZlwaYivS6ZpxJ5gRth2gdG6aY2XVd5fcc/edit  
Using this approach, it is possible to apply both NZW or odd-even, so you can have both behaviour regarding handling inners.   
  
I think in your definition (correct me if I am wrong), this is an algorithm, because it uses the following coordinate specific operations:  
- Calculate the intersection of two segments  
- Checking if two points are equal  
- Performing union and intersecting of polygons (different policies apply here).   
  
At the moment it is a full-correct implementation, also correcting orientation, closing ring, removing invalid points. But i don't suggest integrating this, i think the self-intersecting removal is the only thing needed for boost geometry.   
  
I think also a function like this should be part of boost geometry, many projects can benefit from this.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2021-06-16 08:42:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-862172675  

Hi Wouter, thank you for approaching us!  
  
It looks promising. I will look into it in more detail. I didn't look at our `dissolve` extension for quite a while, sorry if it was broken somehow. We planned to include it at some point, but now, we can maybe better select your approach which looks simpler. And if it is 100% correct - awesome!  
  
What about the "BEER-WARE" license :smiley: can it be changed into Boost Software License (your name can stay there, of course)?

---

## Comment 4

> Username: kleunen  
> Created at: 2021-06-16 08:57:05 UTC  
> Updated at: 2021-06-16 08:57:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-862182761  

> What about the "BEER-WARE" license 😃 can it be changed into Boost Software License (your name can stay there, of course)?  
  
yes, of course. But you owe me a biertje, ok ? :D  
  
It seems the topic became very relevant recently. Projects working on this and some recent scientific papers.   
one thing to check if maybe any of the authors of the paper filed any patents.

---

## Comment 5

> Username: barendgehrels  
> Created at: 2021-06-16 09:31:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-862205854  

Sure, I owe you a biertje!  :smiley:   
  
Will you check patents?  
  
Indeed I've troubles myself compiling dissolve too, we changed quite some strategies internally, and the extensions are sometimes not updated accordingly - there is no automatic CI for them. This makes it a bit harder to compare the algorithms.

---

## Comment 6

> Username: kleunen  
> Created at: 2021-06-16 09:47:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-862216303  

Quick search show, there are patents, related to self-intersecting polygons, but some are chinese:   
  
https://patents.google.com/patent/US20200160569A1/en?q=self-intersecting+polygon&oq=self-intersecting+polygon+  
  
https://patents.google.com/patent/CN112053622B/en?q=self-intersecting+polygon&oq=self-intersecting+polygon+  
  
https://patents.google.com/patent/CN107562779B/en?q=self-intersecting+polygon&oq=self-intersecting+polygon+  
  
https://patents.google.com/patent/US20020145616A1/en?q=self-intersecting+polygon&oq=self-intersecting+polygon+  
  
If you can compare the results, i would be interested to hear about your findings.

---

## Comment 7

> Username: barendgehrels  
> Created at: 2021-06-16 12:28:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-862337670  

I did a comparison in terms of results and of performance. In short I think we can use this new algorithm. I like the two variants (correct and correct_odd_even). I agree with @awulkiew that the namings are not convenient, but we can fix that later.  
  
I did not (yet) repair `dissolve`, but I took changelist `eefd70fcb` which is around 1.5 year old. That version worked for me (it's the last sha where I touched it).  
  
This is my sample program, it's straightforward and not sophisticated  
  
```  
#include <boost/geometry/extensions/algorithms/dissolve.hpp>  
#include <boost/geometry/extensions/multi/algorithms/dissolve.hpp>  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
#include <boost/geometry/geometries/multi_polygon.hpp>  
  
#include "boost_geometry_correct/correct.hpp"  
#include "boost_geometry_correct/data/CLC2006_180927.wkt.cpp"  
  
#include <iostream>  
#include <fstream>  
#include <chrono>  
#include <random>  
  
template <typename Mp>  
void map(std::string const& name, Mp const& mp, Mp const& result)  
{  
  using point = typename boost::geometry::point_type<Mp>::type;  
  
  std::string const filename = std::string("/tmp/svg/") + name + ".svg";  
  std::ofstream svg(filename.c_str());  
  
  boost::geometry::svg_mapper<point> mapper(svg, 400, 400);  
  
  mapper.add(mp);  
  mapper.add(result);  
  
  // Original, light yellow  
  mapper.map(mp, "fill-opacity:0.3;fill:rgb(255,255,192);stroke:rgb(255,255,0);" "stroke-width:1");  
  // Fixed, blue on top  
  mapper.map(result, "fill-opacity:0.6;fill:rgb(0,0,255);stroke:rgb(0,0,128);" "stroke-width:2");  
}  
  
template <typename Mp>  
void measure_performance(std::string const& name, Mp const& mp, char option, int count)  
{  
  auto const start = std::chrono::steady_clock::now();  
  
  const double remove_spike_threshold = 1E-12;  
  for (int i = 0; i < count; i++)  
  {  
    Mp result;  
    switch(option)  
    {  
      case 'd' : boost::geometry::dissolve(mp, result); break;  
      case 'c' : geometry::correct(mp, result, remove_spike_threshold); break;  
      case 'o' : geometry::correct_odd_even(mp, result, remove_spike_threshold); break;  
    }  
  }  
  auto const end = std::chrono::steady_clock::now();  
  auto const ms = std::chrono::duration_cast<std::chrono::milliseconds>(end - start).count();  
  std::cout << name << " " << option << " time: " << ms / 1000.0 << std::endl;  
}  
  
void compare_fix_methods(std::string const& name, std::string const& wkt, int count = 0)  
{  
  using point = boost::geometry::model::d2::point_xy<double>;  
  using polygon = boost::geometry::model::polygon<point>;  
  using multi_polygon = boost::geometry::model::multi_polygon<polygon>;  
  
  multi_polygon mp, dissolved, corrected, oddeven;  
  
  boost::geometry::read_wkt(wkt, mp);  
  
  boost::geometry::dissolve(mp, dissolved);  
  
  double const remove_spike_threshold = 1E-12;  
  geometry::correct(mp, corrected, remove_spike_threshold);  
  geometry::correct_odd_even(mp, oddeven, remove_spike_threshold);  
  
  auto valid_str = [](bool v) { return v ? "valid" : "invalid"; };  
  std::cout << name << " areas " << boost::geometry::area(mp)  
  
            << " d " << boost::geometry::area(dissolved)  
            << " " << valid_str(boost::geometry::is_valid(dissolved))  
  
            << " c " << boost::geometry::area(corrected)  
            << " " << valid_str(boost::geometry::is_valid(corrected))  
  
            << " o " << boost::geometry::area(oddeven)  
            << " " << valid_str(boost::geometry::is_valid(oddeven))  
            << std::endl;  
  
   map(name + "_d", mp, dissolved);  
   map(name + "_c", mp, corrected);  
   map(name + "_o", mp, oddeven);  
  
   if (count > 0)  
   {  
     measure_performance(name, mp, 'd', count);  
     measure_performance(name, mp, 'c', count);  
     measure_performance(name, mp, 'o', count);  
   }  
   std::cout << std::endl;  
}  
  
// Generates a random polygon  
std::string get_random(long long seed)  
{  
  std::mt19937 engine(seed);  
  auto real_rand = std::bind(std::uniform_real_distribution<double>(0,1), engine);  
  std::string first;  
  std::string result = "MULTIPOLYGON(((";  
  for (int i = 0; i < 100; i++)  
  {  
    std::ostringstream out;  
    out << real_rand() << " " << real_rand();  
    if (i > 0) { result += ","; } else { first = out.str(); }  
    result += out.str();  
  }  
  // Close and finish it  
  result += "," + first + ")))";  
  
  return result;  
}  
  
int main()  
{  
  compare_fix_methods("pentagram", "MULTIPOLYGON(((5 0, 2.5 9, 9.5 3.5, 0.5 3.5, 7.5 9, 5 0)))", 10000);  
  compare_fix_methods("complex", "MULTIPOLYGON(((55 10, 141 237, 249 23, 21 171, 252 169, 24 89, 266 73, 55 10)))", 10000);  
  compare_fix_methods("multiple", "MULTIPOLYGON(((0 0, 10 0, 0 10, 10 10, 0 0, 5 0, 5 10, 0 10, 0 5, 10 5, 10 0, 0 0)))", 10000);  
  compare_fix_methods("overlapping", "MULTIPOLYGON (((10 70, 90 70, 90 50, 30 50, 30 30, 50 30, 50 90, 70 90, 70 10, 10 10, 10 70)))", 10000);  
  
  compare_fix_methods("random42", get_random(42), 10);  
  compare_fix_methods("random123", get_random(123), 10);  
  compare_fix_methods("large", wkt_CLC2006_180927, 10);  
  
  return 0;  
}  
  
```  
  
It can be compiled, for example, like this `clang  -O3 verify_dissolve.cpp -I ~/git_private/boost_work/ -I ~/git_other -std=c++14 -lstdc++ -lm`  
  
The results are (noted as **d**issolved, **c**orrect, correct **o**/e, first area/validity of results, then times in milliseconds):  
```  
pentagram areas 33.5 d 25.6158 valid c 25.6158 valid o 17.7317 valid  
pentagram d time: 0.067  
pentagram c time: 0.155  
pentagram o time: 0.552  
  
complex areas 23248.5 d 21123.3 valid c 20252.5 valid o 17256.4 valid  
complex d time: 1.467  
complex c time: 0.4  
complex o time: 2.712  
  
multiple areas 0 d 37.5 valid c 25 valid o 37.5 valid  
multiple d time: 1.597  
multiple c time: 1.137  
multiple o time: 8.138  
  
overlapping areas 4400 d 4000 valid c 4000 valid o 3600 valid  
overlapping d time: 0.095  
overlapping c time: 0.173  
overlapping o time: 0.959  
  
random42 areas 0.397102 d 0.10757 invalid c 0.317975 valid o 0.370735 valid  
random42 d time: 0.025  
random42 c time: 0.137  
random42 o time: 27.539  
  
random123 areas 0.505316 d 0.301726 invalid c 0.400009 valid o 0.362924 valid  
random123 d time: 0.027  
random123 c time: 0.167  
random123 o time: 33.87  
  
large areas 0.681723 d 0.681723 valid c 0.68125 invalid o 0.681723 invalid  
large d time: 24.462  
large c time: 8.629  
large o time: 19.46  
  
```  
  
svgs (generated in `/tmp/svg`) give impressions of the correctness.  
  
The performance depends on the input. This is more often the case. Sometimes dissolve is faster, sometimes correct is faster. The `correct_odd_even` is usually (but not always) the slowest.  
  
For the two random generated polygons, `dissolve` doesn't work correctly (visually) and its result is invalid. The version of `correct` looks wrong too, but is marked as valid. Only the `odd_even` version looks correctly to me.  
  
The `large areas` is using `wkt_CLC2006_180927` (I changed it into a MULTI, it's the only change I made). It somehow lists the result as invalid (maybe I missed something) for correct and odd_even.  
  
In summary: no version is just better, we can investigate more, and maybe even publish both algorithms. The `correct` uses repetitive calls of `intersects` and `union` (I tried to avoid that in dissolve) and that is a reason why it can be slower. Maybe it can be enhanced (didn't look at that). The results of `correct_odd_even` look the best and most useful to me. `dissolve` sometimes fails, and should be repaired. But it is often quicker (at this moment). I know that some users are using it, so we should (probably) keep it unless they are fine with alternatives too.

---

## Comment 8

> Username: barendgehrels  
> Created at: 2021-06-16 12:31:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-862339922  

One of the randomly generated polygons (yellow: input, blue: the result) of:  
  
`dissolve`  
![random42_d](https://user-images.githubusercontent.com/334849/122219117-67bd7400-ceaf-11eb-91fe-80d2d913a336.png)  
  
`correct`  
![random42_c](https://user-images.githubusercontent.com/334849/122219120-68560a80-ceaf-11eb-888d-6c719f75bb63.png)  
  
`correct_odd_even`  
![random42_o](https://user-images.githubusercontent.com/334849/122219110-6724dd80-ceaf-11eb-8549-14dfda8164a2.png)

---

## Comment 9

> Username: barendgehrels  
> Created at: 2021-06-16 12:38:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-862344482  

I can include more pictures on request, but you can also generate them yourselves.  
  
And it would actually be useful to get more realistic random examples (for example with generated stars on random overlapping places and fix those, I will probably adapt the `random_ellipses_stars` test for that)

---

## Comment 10

> Username: kleunen  
> Created at: 2021-06-16 12:39:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-862345201  

Yes, odd-even looks the most correct in this case.   
  
This document describes also a lot of test cases which really show the differences in how the union/overlapping regions can be handled:  
https://docs.google.com/document/d/19YEQS0goSpZlwaYivS6ZpxJ5gRth2gdG6aY2XVd5fcc/edit  
  
Different approaches to union/intersect will result in different outputs. It is difficult to say what is correct and what is not.   
  
Also, regarding the union, i now perform the "union" myself:  
https://github.com/kleunen/boost_geometry_correct/blob/main/correct.hpp#L25-L47  
  
Ideally, i would just like to use a normal union. But this does slow down the approach severely.   
So the union/intersect is still something to look at.

---

## Comment 11

> Username: kleunen  
> Created at: 2021-06-16 12:42:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-862346767  

maybe it is not that strange to provide a "decompose into simple polygons" functions. And implement correct based on this, but also allow the user to just get hold of the decomposed polygon him/herself.

---

## Comment 12

> Username: kleunen  
> Created at: 2021-06-16 13:05:17 UTC  
> Updated at: 2021-06-16 13:17:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-862362927  

In this case:   
![image](https://user-images.githubusercontent.com/230050/122223994-f7652180-ceb3-11eb-88de-3ace9ef78f71.png)  
  
the non-zero winding is calculated based on the _generated_ polygon. So you only have an outer, with an inner inside. But I guess the winding should be based on the _input_ polygon. I am not sure how to do this easily, do i have to perform a raytracing for this, or is there some other way ?  
  
https://github.com/kleunen/boost_geometry_correct/blob/main/correct.hpp#L290-L301  
  
I guess maybe some sweep over the generated polygons should be performed

---

## Comment 13

> Username: kleunen  
> Created at: 2021-06-16 13:24:26 UTC  
> Updated at: 2021-06-16 13:24:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-862377379  

The is also the possibility to always combine an outer as an outer. In case of the random generated polygon, this would mean, the complete outer perimeter of the polygon is traced and the inside is completely filled. This is fast, because you can just union the generated polygons. No need to detect winding/odd-even.

---

## Comment 14

> Username: awulkiew  
> Created at: 2021-06-16 14:54:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-862450315  

One way would be to provide a "complete" solution, several ways of calculating this resulting valid polygon. Then the user would be able to choose the one that is the most suitable for a specific application.  
  
Another way would be the practical approach. Instead of considering a totally random polygon which could probably only be the result of an error in an algorithm or some garbage from memory we could think about the reasons why we may be forced to deal with self-intersections in practice. Why a polygon may be self-intersecting? Is it a result of some faulty algorithm? Is it a human-error while creating areas in OSM? Can this polygon be arbitrary or would there be e.g. several classes of self-intersecting polygons? What the library users will the most typically deal with? Etc.  
  
For instance, is there somewhere a collection of self-intersecting polygons that can be found in OSM right now?  
  
From the top of my head I can think of two classes of self-intersectiong polygons which I think are the most common (I may be wrong).  
  
- Polygons representing holes with the exterior ring. This could be a valid result of some algorithm from other library or polygon created by a user that deosn't know how to create holes:  
  
![path833](https://user-images.githubusercontent.com/1226951/122237837-ed492000-cebf-11eb-8f67-0786d60a41d0.png)  
  
- Vertices very close to each other causing edges to self-intersect, the result of a numerical error or caused by a user.  
  
![path833-3](https://user-images.githubusercontent.com/1226951/122238947-d6ef9400-cec0-11eb-8458-da504259412a.png)  
  
The first case is simple, parts of the exterior ring has to be converted into interior rings. The second one is more ambiguous. I'd argue that what should probably happen would be to preserve all interiors and exteriors based on winding which means that the small triangle on the upper right would be removed and the small triangle on the left would be preserved and merged with the rest of the interior. So the result would be a polygon with a hole.  
  
It'd argue that it'd be sufficient for an algorithm to cover these plus maybe several other classes of self-intersections found in practice (maybe you can think about more or get them from the OSM). If I had to choose between several algorithms successfully dealing with these I'd probably choose the one of the least complexity or the fastest for my specific use case.

---

## Comment 15

> Username: kleunen  
> Created at: 2021-06-16 17:47:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-862582240  

@awulkiew On one hand, yes, you are right. In OSM data indeed many of these errors are quite simple. I think they are caused by GPS devices that are used to take the measurement, indeed sometimes the user enters them correctly. And some data is retrieved from satellite images which are converted to polygons.   
  
The errors you describe are indeed very common. And also sometimes multipolygons are drawn like one polygon:  
  
![image](https://user-images.githubusercontent.com/230050/122265459-1033fe00-ced9-11eb-9b58-a670c7c9e889.png)  
  
Spikes are also very common, that is why I added also a spike removal threshold. Spikes are usually two completely overlapping segments. They result in a ABA type sub polygon after intersection removal, and they have no significant area.   
  
Indeed performance is important. In the OSM data there are actually many self-intersecting polygons. Many of them, actually, render just fine. Because there javascript libraries can handle many cases of self-intersecting polygons. Some, however, are not rendered ok. And this means you will have a large region covered with some rogue polygons.   
  
The approach I implemented now in the default correct, handles these cases you mention correctly. If the sub polygon is oriented clockwise, it will become an outer. If it is CCW, it will become an inner.   
  
If you want to fully correctly handle non-zero winding, you may need to implement raytracing. This may affect performance very negatively. It seems the odd-even approach, is fully correct according to how odd-even should behave. If I benchmark this, on actual geo data, the polygon that @barendgehrels describes, both the odd-even and non-zero winding approach finish in 1600ms on my mobile cpu / slow / embedded linux device. Probably because there are self-intersection in the outer in this data, but actually no overlapping inners or whatever. So it actually suprises me that in the benchmark of barend there is a difference in performance.

---

## Comment 16

> Username: kleunen  
> Created at: 2021-06-16 18:29:39 UTC  
> Updated at: 2021-06-16 18:39:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-862613702  

I think it is possible to calculate the NZW rule without too much negative performance impact. It only applies to overlapping polygons. And because all self-intersection is removed, the polygons are not intersecting anymore. So you can put them in an rtree, ask for overlapping polygons, and basicly calculate the position (inside or out) of this polygon based on the orientation of the rings around.   
  
I guess this can also speed up odd-even, because this needs to be calculated only for regions which are overlapping. Other regions you can just union together.

---

## Comment 17

> Username: kleunen  
> Created at: 2021-06-17 06:50:24 UTC  
> Updated at: 2021-06-17 06:50:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-862978673  

I will have a look to see how this combining can be improved

---

## Comment 18

> Username: awulkiew  
> Created at: 2021-06-17 10:54:37 UTC  
> Updated at: 2021-06-17 10:55:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-863139621  

> And also sometimes multipolygons are drawn like one polygon:  
  
This means that for my second example the small triangle should be preserved because this might be a user drawing multipolygon like that.  
  
![122238947-d6ef9400-cec0-11eb-8458-da504259412a](https://user-images.githubusercontent.com/1226951/122383147-dca1b400-cf6a-11eb-9050-6128b60be382.png)  
  
In general what OSM users will do is going to depend on how the polygon is displayed in the app they're using for editing and how the tiles are rendered on the official website. So basically the expected self-intersections will be those which Potlatch and Mappnik (and maybe JOSM) show well.  
  
But that's only one use-case.  
  
Btw, my images were generated with inkscape. So this program displays the self-intersecting polygons the same way that we're describing here too.

---

## Comment 19

> Username: kleunen  
> Created at: 2021-06-17 11:11:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-863150232  

Yes, this little area in the corner. It really depends on the situation if you want to keep it. Sometimes you have these small areas near the corners of building, and in that case, you want to remove it. Sometimes, this area is a whole part of the building, and you want to keep it. So you can play a little with this "spike" removal, and remove these small non-significant areas. And hopefully, you will get it right in most cases.   
  
Yes, you are right. In the OSM case, you want to follow the behaviour of the editor.

---

## Comment 20

> Username: kleunen  
> Created at: 2021-06-17 18:31:37 UTC  
> Updated at: 2021-06-18 09:53:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-863469038  

I have polygons:  
A: POLYGON((70 50,70 10,10 10,10 70,50 70,50 90,70 90,70 70,90 70,90 50,70 50))  
B: POLYGON((50 50,50 70,70 70,70 50,50 50))  
C: POLYGON((50 50,50 30,30 30,30 50,50 50))  
   
When I use sym difference (Xor) as follows:  
C xor B xor A   
I get:  
MULTIPOLYGON(((70 50,70 10,10 10,10 70,50 70,50 90,70 90,70 70,90 70,90 50,70 50),(70 50,70 70,50 70,50 50,70 50),(50 50,30 50,30 30,50 30,50 50)))  
And polygon is not valid: Geometry has disconnected interior  
  
And   
  
When I use sym difference (Xor) as follows:  
A xor B xor C  
I get:  
MULTIPOLYGON(((70 50,70 70,90 70,90 50,70 50)),((70 70,50 70,50 90,70 90,70 70)),((50 50,70 50,70 10,10 10,10 70,50 70,50 50),(50 50,30 50,30 30,50 30,50 50)))  
And polygon is valid  
  
Byte with sym. difference, order should not make a difference, right ?   
A sym.difference B = B sym.difference A

---

## Comment 21

> Username: kleunen  
> Created at: 2021-06-18 15:06:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-864104403  

I fixed the non-zero winding, this is now :  
  
random42 non-zero winding:  
![image](https://user-images.githubusercontent.com/230050/122581446-58792a80-d057-11eb-9a33-ff4332e5eed0.png)  
  
That looks correct. Speed odd-even is now improved. Non-zero winding is now slower :)  
I have to see if some further optimization can be done, but I think the filling is now correct like in SVG.

---

## Comment 22

> Username: kleunen  
> Created at: 2021-06-18 18:25:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-864206527  

I optimized now the non-zero winding approach, but #869 is now preventing me from further optimizing the odd-even approach. @barendgehrels can you maybe run a benchmark again on your machine ?

---

## Comment 23

> Username: barendgehrels  
> Created at: 2021-06-18 19:39:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-864238337  

@kleunen thanks for all your efforts, messages and improvements! I currently don't work daily on this project but I've read the messages, cool. @awulkiew thanks for your input!  
  
I'll run the benchmark again this weekend (this is another computer).

---

## Comment 24

> Username: kleunen  
> Created at: 2021-06-19 08:58:27 UTC  
> Updated at: 2021-06-19 08:59:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-864378632  

Clipper defines two additional polygon fill types:  
http://www.angusj.com/delphi/clipper/documentation/Docs/Units/ClipperLib/Types/PolyFillType.htm  
  
- Even-Odd (Alternate): Odd numbered sub-regions are filled, while even numbered sub-regions are not.  
- Non-Zero (Winding): All non-zero sub-regions are filled.  
- Positive: All sub-regions with winding counts > 0 are filled.  
- Negative: All sub-regions with winding counts < 0 are filled.  
  
I like this positive and negative, but I would define them differently:   
- Positive: All sub-polygons from the outer are filled, all inners are empty  
- Negative: Outers are joined into outer rings, inners are converted to filled  
  
Positive can be generated very quickly, it does not need to calculate the winding. This might be useful when performance is a concern. Negative i don't see a practical purpose, but if you have positive, why not have negative ?

---

## Comment 25

> Username: barendgehrels  
> Created at: 2021-06-19 09:45:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-864383367  

Well done! I can confirm that it is faster now, sometimes much faster, and that the `correct` is fixed for the random "junk" polygons  
  
```  
pentagram areas 33.5 d 25.6158 valid c 25.6158 valid o 17.7317 valid  
pentagram d time: 0.066  
pentagram c time: 0.213  
pentagram o time: 0.528  
  
complex areas 23248.5 d 21123.3 valid c 21123.3 valid o 17256.4 valid  
complex d time: 1.407  
complex c time: 0.661  
complex o time: 2.671  
  
multiple areas 0 d 37.5 valid c 50 valid o 37.5 valid  
multiple d time: 1.583  
multiple c time: 1.625  
multiple o time: 1.961  
  
overlapping areas 4400 d 4000 valid c 4000 valid o 3600 valid  
overlapping d time: 0.087  
overlapping c time: 0.223  
overlapping o time: 0.879  
  
random42 areas 0.397102 d 0.10757 invalid c 0.540874 valid o 0.370735 valid  
random42 d time: 0.024  
random42 c time: 0.637  
random42 o time: 1.595  
  
random123 areas 0.505316 d 0.301726 invalid c 0.510328 valid o 0.362924 valid  
random123 d time: 0.026  
random123 c time: 0.828  
random123 o time: 1.847  
  
large areas 0.681723 d 0.681723 valid c 0.68125 invalid o 0.681723 invalid  
large d time: 23.29  
large c time: 8.389  
large o time: 14.641  
```

---

## Comment 26

> Username: kleunen  
> Created at: 2021-06-19 09:56:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-864384398  

The odd even approach can have the same performance as the other approach. But for this, the difference should be fixed.

---

## Comment 27

> Username: barendgehrels  
> Created at: 2021-06-19 09:57:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-864384452  

I'm looking at the code. It might be possible to change `dissolve_find_intersections` into just using `detail::self_get_turn_points::self_turns`. You will then get all turns back, and you can iterate through them to assign the `pseudo_vertices` you need.  
  
I don't know if it is faster, but it uses the more standard way.   
  
Anyway, it's just a question, I'm just curious to the difference. It is not really necessary to change it. I added some statements and the current method looks fast anyway.

---

## Comment 28

> Username: barendgehrels  
> Created at: 2021-06-19 09:57:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-864384521  

> The odd even approach can have the same performance as the other approach. But for this, the difference should be fixed.  
  
Yes I noticed the new issue. Will look at it.

---

## Comment 29

> Username: kleunen  
> Created at: 2021-06-19 10:19:42 UTC  
> Updated at: 2021-06-19 11:03:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-864386759  

> I'm looking at the code. It might be possible to change `dissolve_find_intersections` into just using `detail::self_get_turn_points::self_turns`. You will then get all turns back, and you can iterate through them to assign the `pseudo_vertices` you need.  
>   
> I don't know if it is faster, but it uses the more standard way.  
>   
> Anyway, it's just a question, I'm just curious to the difference. It is not really necessary to change it. I added some statements and the current method looks fast anyway.  
  
Yes it sounds like this function can be used. The other things is that i basicly crafted my own union which checks intersection before performing the union. This is much faster than the standard union. But i dont understand why.  
  
https://github.com/kleunen/boost_geometry_correct/blob/main/correct.hpp#L27-L49  
  
It adds a polygon to an existing multipolygon.

---

## Comment 30

> Username: kleunen  
> Created at: 2021-06-19 12:13:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-864398300  

> I'm looking at the code. It might be possible to change `dissolve_find_intersections` into just using `detail::self_get_turn_points::self_turns`. You will then get all turns back, and you can iterate through them to assign the `pseudo_vertices` you need.  
>   
> I don't know if it is faster, but it uses the more standard way.  
>   
> Anyway, it's just a question, I'm just curious to the difference. It is not really necessary to change it. I added some statements and the current method looks fast anyway.  
  
I tried this, but I think this algorithm misses the cases when two segments have multiple intersection points (they are collinear). Is that possible ? It only returns 1 turn in that case, while 2 turns should be found.

---

## Comment 31

> Username: barendgehrels  
> Created at: 2021-06-19 13:52:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-864409347  

It can return 2 turns, probably you have to change the policy. There is a policy to get back all turns (that is not the default policy). But you might get some duplicates in that case (where a segment arrives at another segment, and then starts from it)

---

## Comment 32

> Username: kleunen  
> Created at: 2021-06-19 13:54:34 UTC  
> Updated at: 2021-06-19 14:41:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-864409619  

> It can return 2 turns, probably you have to change the policy. There is a policy to get back all turns (that is not the default policy). But you might get some duplicates in that case (where a segment arrives at another segment, and then starts from it)  
  
Ok. This is no an issue  
  
I think i figured out the policy

---

## Comment 33

> Username: kleunen  
> Created at: 2021-06-20 14:35:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-864564249  

I added generation of the reference cases (the polygons only for now), from this document:  
https://docs.google.com/document/d/19YEQS0goSpZlwaYivS6ZpxJ5gRth2gdG6aY2XVd5fcc/edit  
  
My NZW approach generates the same output as JTS - Buffer(0) approach  
My Odd-even approach generates the same output as GEOS - Makevalid  
  
Only difference is the handling of the inners. GEOS uses sym_difference to combine outers with the inners. So inners might become outers again. I use difference now, inners always stay holes, even after "repair".   
  
I am not sure what would be the "correct" behaviour.   
  
You can see in this case, part of the hole is outside the polygon. This does not become a separate polygon (yellow block is an inner):  
![image](https://user-images.githubusercontent.com/230050/122678049-5349e600-d1e5-11eb-9fcc-9b664bba62f8.png)  
  
This is what geos - makevalid generates:  
![image](https://user-images.githubusercontent.com/230050/122678077-78d6ef80-d1e5-11eb-967d-05899acf0a7d.png)

---

## Comment 34

> Username: barendgehrels  
> Created at: 2021-06-23 08:41:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-866647808  

> I am not sure what would be the "correct" behaviour.  
  
There are many ways to handle this and it depends on the situation if it is correct, or not. Therefore I like the different rules / possibilities you implemented, such that users have some choice. In this particular case the GEOS looks better - but just because of the look. If the right polygon was indeed intended as "negative" then it makes sense to not include it.

---

## Comment 35

> Username: barendgehrels  
> Created at: 2021-06-23 08:50:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-866654407  

> I think i figured out the policy  
  
You did! :+1:

---

## Comment 36

> Username: kleunen  
> Created at: 2021-06-23 09:14:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-866671660  

> > I am not sure what would be the "correct" behaviour.  
>   
> There are many ways to handle this and it depends on the situation if it is correct, or not. Therefore I like the different rules / possibilities you implemented, such that users have some choice. In this particular case the GEOS looks better - but just because of the look. If the right polygon was indeed intended as "negative" then it makes sense to not include it.  
  
Then possibly we should make this behaviour configurable. So you have the filling behaviour, the combining of outer/inners and the combining of polygons within a multipolygon. We can make some sensible default configurations, but allow the user to define other behaviours as well.

---

## Comment 37

> Username: barendgehrels  
> Created at: 2021-06-23 09:19:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-866675512  

> Then possibly we should make this behaviour configurable. So you have the filling behaviour, the combining of outer/inners and the combining of polygons within a multipolygon. We can make some sensible default configurations, but allow the user to define other behaviours as well.  
  
That sounds awesome.

---

## Comment 38

> Username: kleunen  
> Created at: 2021-06-23 09:28:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-866682080  

But can you comment on, why this union is so much faster than the normal union:  
https://github.com/kleunen/boost_geometry_correct/blob/main/correct.hpp#L31-L53  
  
Ideally I would use a divide/conquer loop like this:  
https://github.com/kleunen/boost_geometry_correct/blob/main/correct.hpp#L405-L416  
  
Because if the combine function is configurable to be union/sym_difference:  
https://github.com/kleunen/boost_geometry_correct/blob/main/correct.hpp#L411  
  
This is easier.   
  
Is it because less memory allocations are performed, because the union is "in-place" ?

---

## Comment 39

> Username: barendgehrels  
> Created at: 2021-06-23 10:15:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-866712833  

> But can you comment on, why this union is so much faster than the normal union:  
  
I don't know it at this moment... It is a normal union, by the way. So what would be the alternative? We can only combine two polygons with a union, like you do. We can't combine multiple polygons.

---

## Comment 40

> Username: barendgehrels  
> Created at: 2021-06-23 10:18:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-866714883  

> Ideally I would use a divide/conquer loop like this:  
  
I don't understand all your code yet. By the way, the sorting lambda recalculates the area (probably) multiple times, that might be optimized.

---

## Comment 41

> Username: kleunen  
> Created at: 2021-06-23 10:25:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-866719773  

> > Ideally I would use a divide/conquer loop like this:  
>   
> I don't understand all your code yet. By the way, the sorting lambda recalculates the area (probably) multiple times, that might be optimized.  
  
yes, that is something to optimize.   
  
well, the alternative for the union, if you perform a union of a multipolygon, with a polygon. And the multipolygon has, say, 10 polygons in there, but only 1 intersects. You only have to update 1 polygon within the multipolygon. But now, i think, all 10 are copied to the output. But ideally, you want to "move" them to the output, if the input multipolygon is not relevant anymore.   
  
Not this:  
````C++  
union_(mp, p, output)  
````  
  
but this:  
````C++  
union_(std::move(mp), std::move(p), output)  
````

---

## Comment 42

> Username: barendgehrels  
> Created at: 2021-06-23 10:44:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-866731257  

> well, the alternative for the union, if you perform a union of a multipolygon, with a polygon. And the multipolygon has, say, 10 polygons in there, but only 1 intersects. You only have to update 1 polygon within the multipolygon. But now, i think, all 10 are copied to the output. But ideally, you want to "move" them to the output, if the input multipolygon is not relevant anymore.  
  
I think I understand. But our code doesn't work like that. It leaves all input untouched, even if you don't need it anymore. So yes, then you have to implement a hand crafted version like you did.

---

## Comment 43

> Username: kleunen  
> Created at: 2021-06-23 13:57:01 UTC  
> Updated at: 2021-06-23 17:50:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-866858905  

I made the combine/difference configurable, so the sym_difference can be used now in odd/even. I think the default output is now the same as GEOS (for odd/even).    
  
I don't see that much difference in performance, only for this large polygon (WKT) i import from file.   
On my machine this goes from 1600ms to 6000ms.   
  
*edit* found the performance regression

---

## Comment 44

> Username: kleunen  
> Created at: 2021-06-26 08:01:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-868965369  

It seems there is still an issue with multiple intersections, if multiple segments intersect at the same point. One paper, does not mention this issue at all. The other paper states you can select any outgoing edge and it does not matter. This appears to be true, to some extent. It works, but you may end up at a different ring, and the generated ring may contain a region at the start which overlaps. This can be handled, but it is not so nice.   
  
Both papers test with random generated polygons. The probability of a multiple intersection is very low in this case, probably that is why it works in these papers. The test set from GEOS contains "hand" drawn polygons with multiple intersections.

---

## Comment 45

> Username: kleunen  
> Created at: 2021-06-28 07:29:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-869431846  

I updated the approach to handle multiple intersections correctly. Possibly the approach can be made more robust, but this requires quite a different approach. So this needs quite some rework and testing and not sure if it really is better. I think the implementation is very solid now and configurable. But would be good to perform more testing.

---

## Comment 46

> Username: barendgehrels  
> Created at: 2021-06-30 13:44:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-871417770  

> I updated the approach to handle multiple intersections correctly. Possibly the approach can be made more robust, but this requires quite a different approach. So this needs quite some rework and testing and not sure if it really is better. I think the implementation is very solid now and configurable. But would be good to perform more testing.  
  
Sounds good!  
  
I hope to have a PR of the difference-issue you found next week. Are you still dependent on it?

---

## Comment 47

> Username: kleunen  
> Created at: 2021-06-30 17:29:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-871595457  

> Are you still dependent on it?  
  
yes, this is required for the odd/even filling.

---

## Comment 48

> Username: kleunen  
> Created at: 2021-07-08 16:35:04 UTC  
> Updated at: 2021-07-08 16:37:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-876583125  

With the current head, the self_turns call results in the following error:   
````  
/home/kleunen/boost/boost/geometry/algorithms/detail/disjoint/box_box.hpp:64:27: error: no matching function for call to ‘boost::geometry::strategy::intersection::cartesian_segments<>::apply(const boost::geometry::model::box<boost::geometry::model::d2::point_xy<double> >&, const boost::geometry::model::box<boost::geometry::model::d2::point_xy<double> >&)’  
   64 |     return Strategy::apply(box1, box2);  
````  
  
Is this related to #870 ?  
  
Ah yes, i now defined:  
````  
boost::geometry::strategies::cartesian<> strategy;

---

## Comment 49

> Username: kleunen  
> Created at: 2021-07-08 16:51:05 UTC  
> Updated at: 2021-07-08 16:54:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-876592763  

The is odd-even combining when I sort in reverse order:   
![image](https://user-images.githubusercontent.com/230050/124961053-49b1e200-e01d-11eb-8e1f-bc55921a9997.png)  
  
I think there is still an issue with sym_difference. Because the order in which I apply, should not make a difference. But this may not be a good test case :)

---

## Comment 50

> Username: barendgehrels  
> Created at: 2025-07-19 13:51:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/868#issuecomment-3092362362  

Hi Wouter!  
  
After a long time...  
  
... now used in https://github.com/boostorg/geometry/pull/1411
