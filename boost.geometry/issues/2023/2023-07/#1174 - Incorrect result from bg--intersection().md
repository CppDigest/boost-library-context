# #1174 - Incorrect result from bg::intersection() [Open]

> Username: dratchkov  
> Created at: 2023-07-11 02:30:03 UTC  
> Updated at: 2023-09-13 19:44:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/1174  

I see there are few reports against this function already, I hope I am not repeating the issue.  
  
I have an oval, which I am trying to split into 4 pieces using the 4 quadrants from the center. The result is that my lower left quadrant is incorrect (same as the overlapping box), upper right is incorrect (empty), but upper left and lower right are correct.  
  
This is the input oval:  
![image](https://github.com/boostorg/geometry/assets/12432089/cd9fb7dd-4fa5-4d71-9d10-2187344bc2aa)  
  
And this is the final result:  
![image](https://github.com/boostorg/geometry/assets/12432089/fca21f10-1f05-4d7f-a5e9-11ddc6b8e04f)  
  
I am using boost 1.82 with BOOST_GEOMETRY_NO_ROBUSTNESS.  
  
Any suggestions?  
  
Here is my code:  
```  
auto wkt = "POLYGON ((4.67226015 3.73961913997, 4.67226015 3.73961913998, 4.67226015 3.73961913999, 4.67226015 3.73961914, 4.67226015 3.73961914001, 4.67226015 3.73961914003, 4.67226015001 3.73961914004, 4.67226015001 3.73961914005, 4.67593090393 3.76279536818, 4.67593090393 3.76279536819, 4.67593090394 3.7627953682, 4.67593090394 3.76279536822, 4.67593090394 3.76279536823, 4.67593090394 3.76279536824, 4.67593090396 3.76279536827, 4.68658384675 3.78370294571, 4.68658384676 3.78370294572, 4.68658384677 3.78370294574, 4.68658384677 3.78370294575, 4.68658384678 3.78370294576, 4.68658384679 3.78370294577, 4.68658384681 3.78370294579, 4.70317619421 3.80029529319, 4.70317619422 3.8002952932, 4.70317619423 3.80029529321, 4.70317619424 3.80029529322, 4.70317619425 3.80029529323, 4.70317619426 3.80029529323, 4.70317619429 3.80029529325, 4.72408377174 3.81094823605, 4.72408377175 3.81094823605, 4.72408377177 3.81094823606, 4.72408377178 3.81094823606, 4.72408377179 3.81094823606, 4.7240837718 3.81094823606, 4.7240837718 3.81094823607, 4.72408377181 3.81094823607, 4.72408377182 3.81094823607, 4.74725999995 3.81461899, 4.74725999996 3.81461899, 4.74725999998 3.81461899, 4.74725999999 3.81461899, 4.74726 3.81461899, 4.74726000002 3.81461899, 4.74726000003 3.81461899, 4.74726000004 3.81461899, 5.34706575996 3.81461899, 5.34706575997 3.81461899, 5.34706575998 3.81461899, 5.34706576 3.81461899, 5.34706576002 3.81461899, 5.34706576004 3.81461899, 5.37024198818 3.81094823607, 5.37024198819 3.81094823607, 5.3702419882 3.81094823607, 5.3702419882 3.81094823606, 5.37024198821 3.81094823606, 5.37024198822 3.81094823606, 5.37024198823 3.81094823606, 5.37024198824 3.81094823606, 5.37024198825 3.81094823605, 5.37024198826 3.81094823605, 5.37024198827 3.81094823604, 5.39114956571 3.80029529325, 5.39114956572 3.80029529325, 5.39114956572 3.80029529324, 5.39114956573 3.80029529324, 5.39114956574 3.80029529323, 5.39114956575 3.80029529323, 5.39114956575 3.80029529322, 5.39114956576 3.80029529322, 5.39114956577 3.80029529321, 5.39114956578 3.80029529321, 5.39114956579 3.8002952932, 5.39114956579 3.80029529319, 5.4077419132 3.78370294579, 5.40774191321 3.78370294577, 5.40774191322 3.78370294577, 5.40774191322 3.78370294576, 5.40774191323 3.78370294575, 5.40774191325 3.78370294571, 5.41839485604 3.76279536827, 5.41839485604 3.76279536826, 5.41839485605 3.76279536826, 5.41839485605 3.76279536825, 5.41839485606 3.76279536823, 5.41839485606 3.76279536822, 5.41839485606 3.76279536821, 5.41839485607 3.7627953682, 5.41839485607 3.76279536819, 5.41839485607 3.76279536818, 5.42206560999 3.73961914005, 5.42206560999 3.73961914004, 5.42206561 3.73961914003, 5.42206561 3.73961914002, 5.42206561 3.73961914001, 5.42206561 3.73961914, 5.42206561 3.73961913999, 5.42206561 3.73961913997, 5.42206560999 3.73961913996, 5.42206560999 3.73961913995, 5.41839485607 3.71644291182, 5.41839485607 3.71644291181, 5.41839485607 3.7164429118, 5.41839485606 3.7164429118, 5.41839485606 3.71644291178, 5.41839485606 3.71644291177, 5.41839485606 3.71644291176, 5.41839485606 3.71644291175, 5.41839485604 3.71644291173, 5.40774191325 3.69553533429, 5.40774191324 3.69553533428, 5.40774191324 3.69553533427, 5.40774191323 3.69553533426, 5.40774191323 3.69553533425, 5.40774191322 3.69553533425, 5.40774191322 3.69553533424, 5.40774191321 3.69553533423, 5.4077419132 3.69553533422, 5.40774191319 3.69553533421, 5.39114956577 3.67894298679, 5.39114956576 3.67894298678, 5.39114956575 3.67894298677, 5.39114956571 3.67894298675, 5.37024198827 3.66829004396, 5.37024198826 3.66829004395, 5.37024198825 3.66829004395, 5.37024198824 3.66829004394, 5.37024198823 3.66829004394, 5.37024198822 3.66829004394, 5.37024198821 3.66829004394, 5.3702419882 3.66829004393, 5.37024198819 3.66829004393, 5.37024198818 3.66829004393, 5.34706576005 3.66461929, 5.34706576004 3.66461929, 5.34706576002 3.66461929, 5.34706576001 3.66461929, 5.34706576 3.66461929, 5.34706575998 3.66461929, 5.34706575997 3.66461929, 5.34706575996 3.66461929, 4.74726000004 3.66461929, 4.74726000003 3.66461929, 4.74726000002 3.66461929, 4.74726 3.66461929, 4.74725999998 3.66461929, 4.74725999996 3.66461929, 4.72408377182 3.66829004393, 4.72408377181 3.66829004393, 4.7240837718 3.66829004393, 4.72408377179 3.66829004394, 4.72408377178 3.66829004394, 4.72408377177 3.66829004394, 4.72408377175 3.66829004395, 4.72408377174 3.66829004395, 4.72408377173 3.66829004396, 4.70317619429 3.67894298675, 4.70317619428 3.67894298676, 4.70317619425 3.67894298677, 4.70317619424 3.67894298678, 4.70317619421 3.67894298681, 4.68658384681 3.69553533421, 4.6865838468 3.69553533422, 4.68658384679 3.69553533423, 4.68658384679 3.69553533424, 4.68658384678 3.69553533424, 4.68658384678 3.69553533425, 4.68658384677 3.69553533426, 4.68658384676 3.69553533427, 4.68658384675 3.69553533429, 4.67593090396 3.71644291173, 4.67593090396 3.71644291174, 4.67593090394 3.71644291176, 4.67593090394 3.71644291177, 4.67593090394 3.71644291178, 4.67593090394 3.7164429118, 4.67593090393 3.7164429118, 4.67593090393 3.71644291181, 4.67593090393 3.71644291182, 4.67226015001 3.73961913995, 4.67226015001 3.73961913996, 4.67226015 3.73961913997))";  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/io/wkt/read.hpp>  
  
namespace bg = boost::geometry;  
  
typedef double coord_type;  
typedef bg::model::point<coord_type, 2, bg::cs::cartesian> point_type2d;  
typedef bg::model::box<point_type2d> box_type2d;  
typedef bg::model::polygon<point_type2d> polygon_type2d;  
  
int main(int argc, const char **argv) {  
    polygon_type2d poly;  
    std::vector<polygon_type2d> out;  
    box_type2d bbox;  
  
  
    bg::read_wkt(wkt, poly);  
    bg::correct(poly);  
  
    // find enclosing box  
    bg::envelope(poly, bbox);  
  
    // fix center of enclosing box  
    auto cx = (bbox.max_corner().get<0>() + bbox.min_corner().get<0>()) / 2;  
    auto cy = (bbox.max_corner().get<1>() + bbox.min_corner().get<1>()) / 2;  
  
    // intersect with (llx, lly) (cx, cy)  
    bg::intersection(  
            poly, box_type2d(  
                    {bbox.min_corner().get<0>(), bbox.min_corner().get<1>()},  
                    {cx, cy}),  
            out);  
  
    if(!out.empty()) // result is incorrect  
        std::cout << bg::wkt(out[0]) <<std::endl;  
    else  
        std::cout << "EMPTY\n";  
  
    out.clear();  
  
    // intersect with (cx, lly) (urx, cy)  
    bg::intersection(poly, box_type2d(  
            {cx, bbox.min_corner().get<1>()},  
            {bbox.max_corner().get<0>(), cy}  
    ), out);  
  
    if (!out.empty()) //result is OK  
        std::cout << bg::wkt(out[0]) << std::endl;  
    else  
        std::cout << "EMPTY\n";  
  
    out.clear();  
  
    // intersect with (llx, cy) (cx, ury)  
    bg::intersection(poly, box_type2d(  
            {bbox.min_corner().get<0>(), cy},  
            {cx, bbox.max_corner().get<1>()}  
    ), out);  
  
    if (!out.empty()) // result is OK  
        std::cout << bg::wkt(out[0]) << std::endl;  
    else  
        std::cout << "EMPTY\n";  
  
    out.clear();  
  
    // intersect with (cx, cy) (urx, ury)  
    bg::intersection(poly, box_type2d(  
            {cx, cy},  
            {bbox.max_corner().get<0>(), bbox.max_corner().get<1>()}  
    ), out);  
  
    if (!out.empty()) // result is incorrect, it is empty  
        std::cout << bg::wkt(out[0]) << std::endl;  
    else  
        std::cout << "EMPTY\n";  
  
    return 0;  
}  
```

---

## Comment 1

> Username: awulkiew  
> Created at: 2023-07-22 23:35:57 UTC  
> Updated at: 2023-07-22 23:37:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/1174#issuecomment-1646691423  

Thanks for the report. I can reproduce it (VS2022/msvc-14.3, develop/1.83-beta1).  
  
I don't know if it helps but here are the turns:  
![obraz](https://github.com/boostorg/geometry/assets/1226951/dd77a680-4a84-4e49-9319-446566575b90)  
![obraz](https://github.com/boostorg/geometry/assets/1226951/f4d83b2c-118e-4a5f-bb89-9492ce43ce4d)  
![obraz](https://github.com/boostorg/geometry/assets/1226951/757d5124-543f-471b-8dbe-7e3a31706cc6)  
![obraz](https://github.com/boostorg/geometry/assets/1226951/a1f92faa-b75c-4108-a63d-ba9cf4f6b5f6)

---

## Comment 2

> Username: dratchkov  
> Created at: 2023-07-23 00:46:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/1174#issuecomment-1646712366  

I got this particular case to work by using `boost::multiprecision::cpp_bin_float_50`, but issues popped up elsewhere. I also tried `cpp_bin_float_100`, but still did not get to fully stable results and the performance suffered.  
I know that my input coordinates with be with 1e-5 resolution. Is it possible to use a custom comparator for values, so that I get the speed of float/double and yet stable result?

---

## Comment 3

> Username: dratchkov  
> Created at: 2023-08-31 06:14:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/1174#issuecomment-1700425694  

I thought I'll debug this to help out. I got to the point where I am finding that at differed types for coord_type I get  different number of turns. Is this expected?

---

## Comment 4

> Username: barendgehrels  
> Created at: 2023-09-05 18:30:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/1174#issuecomment-1707115529  

Yes, the number of turns can be different for different coordinate types.  
I will have a look at this issue tomorrow.

---

## Comment 5

> Username: barendgehrels  
> Created at: 2023-09-05 18:36:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/1174#issuecomment-1707124887  

You can omit the flag `BOOST_GEOMETRY_NO_ROBUSTNESS` because it is the default.  
But it will not make a difference in your findings.

---

## Comment 6

> Username: dratchkov  
> Created at: 2023-09-06 01:47:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/1174#issuecomment-1707528135  

It makes sense that turn count would depend on the coordinate type, at least in the general case. In this case, the coordinates are not that close for a double type.  
  
I am curious about this. Can you help me with some pointers into the code I can look to understand a bit more about the turn calculation?

---

## Comment 7

> Username: barendgehrels  
> Created at: 2023-09-06 09:37:27 UTC  
> Updated at: 2023-09-06 09:50:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/1174#issuecomment-1708006958  

The turn points in the oval are so close together that some turns are calculated incorrectly.  
  
These are the enriched turns.  
```  
ENRICH-assign Ring (s:0)  
0 cl=-1 meth=c seg=s:0, v:0 dst=1/1 (1) op=iu (i) nxt=-1 / 1 [vx 1] POINT(4.6722601499999996122 3.7396191399799998401)  
1 cl=-1 meth=c seg=s:0, v:1 dst=1/1 (1) op=ui (u) nxt=-1 / 2 [vx 2] POINT(4.6722601499999996122 3.7396191399899998409)  
2 cl=-1 meth=c seg=s:0, v:2 dst=1/1 (1) op=ui (u) nxt=-1 / 3 [vx 141] POINT(4.6722601499999996122 3.7396191399999998417)  
3 cl=-1 meth=m seg=s:0, v:141 dst=0.02249267101156808904/0.04498534202313617808 (0.5) op=cc (c) nxt=4 / 4 [vx 141] POINT(5.0471628800000001291 3.6646192900000000847)  
4 cl=-1 meth=c seg=s:0, v:141 dst=1/1 (1) op=cc (c) nxt=-1 / 5 [vx 142] POINT(4.7472600000399998166 3.6646192900000000847)  
5 cl=-1 meth=c seg=s:0, v:142 dst=1/1 (1) op=cc (c) nxt=-1 / 6 [vx 143] POINT(4.7472600000299998158 3.6646192900000000847)  
6 cl=-1 meth=c seg=s:0, v:143 dst=1/1 (1) op=cc (c) nxt=-1 / 7 [vx 144] POINT(4.7472600000199998149 3.6646192900000000847)  
7 cl=-1 meth=c seg=s:0, v:144 dst=1/1 (1) op=cc (c) nxt=-1 / 8 [vx 145] POINT(4.7472599999999998133 3.6646192900000000847)  
8 cl=-1 meth=c seg=s:0, v:145 dst=1/1 (1) op=cc (c) nxt=-1 / 9 [vx 146] POINT(4.7472599999799998116 3.6646192900000000847)  
9 cl=-1 meth=c seg=s:0, v:146 dst=1/1 (1) op=iu (i) nxt=-1 / 11 [vx 181] POINT(4.74725999995999981 3.6646192900000000847)  
11 cl=-1 meth=t seg=s:0, v:181 dst=1/1 (1) op=iu (i) nxt=-1 / 0 [vx 0] POINT(4.6722601499999996122 3.7396191399699998392)  
ENRICH-assign Ring (s:1)  
0 cl=-1 meth=c seg=s:1, v:0 dst=0.074999849979999755334/0.074999850000000201078 (0.99999999973332687198) op=iu (u) nxt=1 / 1 [vx 0] POINT(4.6722601499999996122 3.7396191399799998401)  
1 cl=-1 meth=c seg=s:1, v:0 dst=0.074999849989999756161/0.074999850000000201078 (0.99999999986666043839) op=ui (i) nxt=2 / 2 [vx 0] POINT(4.6722601499999996122 3.7396191399899998409)  
2 cl=-1 meth=c seg=s:1, v:0 dst=1/1 (1) op=ui (i) nxt=-1 / 11 [vx 0] POINT(4.6722601499999996122 3.7396191399999998417)  
11 cl=-1 meth=t seg=s:1, v:0 dst=1/1 (1) op=iu (u) nxt=-1 / 3 [vx 2] POINT(4.6722601499999996122 3.7396191399699998392)  
3 cl=-1 meth=m seg=s:1, v:2 dst=1/1 (1) op=cc (c) nxt=-1 / 4 [vx 3] POINT(5.0471628800000001291 3.6646192900000000847)  
4 cl=-1 meth=c seg=s:1, v:3 dst=0.29990287996000031256/0.37490273000000051695 (0.7999485092039737566) op=cc (c) nxt=5 / 5 [vx 3] POINT(4.7472600000399998166 3.6646192900000000847)  
5 cl=-1 meth=c seg=s:1, v:3 dst=0.29990287997000031339/0.37490273000000051695 (0.79994850923064741988) op=cc (c) nxt=6 / 6 [vx 3] POINT(4.7472600000299998158 3.6646192900000000847)  
6 cl=-1 meth=c seg=s:1, v:3 dst=0.29990287998000031422/0.37490273000000051695 (0.79994850925732097213) op=cc (c) nxt=7 / 7 [vx 3] POINT(4.7472600000199998149 3.6646192900000000847)  
7 cl=-1 meth=c seg=s:1, v:3 dst=0.29990288000000031587/0.37490273000000051695 (0.79994850931066818767) op=cc (c) nxt=8 / 8 [vx 3] POINT(4.7472599999999998133 3.6646192900000000847)  
8 cl=-1 meth=c seg=s:1, v:3 dst=0.29990288002000031753/0.37490273000000051695 (0.79994850936401529218) op=cc (c) nxt=9 / 9 [vx 3] POINT(4.7472599999799998116 3.6646192900000000847)  
9 cl=-1 meth=c seg=s:1, v:3 dst=0.29990288004000031918/0.37490273000000051695 (0.79994850941736250771) op=iu (u) nxt=-1 / 0 [vx 0] POINT(4.74725999995999981 3.6646192900000000847)  
```  
  
You get this info by adding these defines, before all other code  
```  
#define BOOST_GEOMETRY_DEBUG_ENRICH  
#define BOOST_GEOMETRY_DEBUG_IDENTIFIER  
#define BOOST_GEOMETRY_DEBUG_SEGMENT_IDENTIFIER  
#define BOOST_GEOMETRY_DEFINE_STREAM_OPERATOR_SEGMENT_RATIO  
```  
(I added the turn point locally).  
  
If I compare a run with `double` and multi precision, they give different information.  
A `c` means: continue, and a `u/i` means that it should take the left or right branch.  
  
![meld](https://github.com/boostorg/geometry/assets/334849/d5966c64-ac7a-4307-b8fb-4bb160f89aac)  
  
  
There might be multiple problems. Currently I cannot continue with this, I would advice you to simplify the input ellipse.

---

## Comment 8

> Username: barendgehrels  
> Created at: 2023-09-13 19:44:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/1174#issuecomment-1718218923  

Alternatively you can increase the boxes, for example  
```  
    bg::intersection(poly, box_type2d(  
            {cx, bbox.min_corner().get<1>() - 0.01},  
            {bbox.max_corner().get<0>() + 0.01, cy}  
    ), out);  
```  
you will get the same result (but most probably without these errors)
