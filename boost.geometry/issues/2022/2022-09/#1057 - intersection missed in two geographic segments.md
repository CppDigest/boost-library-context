# #1057 - intersection missed in two geographic segments [Open]

> Username: barendgehrels  
> Created at: 2022-09-14 10:34:03 UTC  
> Updated at: 2025-07-16 15:43:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/1057  

Hi @vissarion ,  
  
I'm busy with the buffer for linestrings and in some rare cases, it goes wrong because a turn (intersection point) is not calculated.  
  
For example this input (segment p, segment q)  
```  
p1 POINT(10.40012269074711 63.42843206062311)   
p2 POINT(10.4029932904841 63.42811616062505)   
q1 POINT(10.40063675549998 63.42837490942328)   
q2 POINT(10.40063813682225 63.42837538908047)  
```  
  
I would expect an intersection point. But it is not there.  
  
Situation:  
![image](https://user-images.githubusercontent.com/334849/190127744-01d7b603-895c-404f-b97a-0cb1d7a4c8ff.png)  
  
  
But the red line is quite small :-) Zoomed in:  
![image](https://user-images.githubusercontent.com/334849/190127882-224e01a4-2470-4470-8c93-b18c29d64e43.png)  
  
  
So the lines clearly intersect. At least in Cartesian (I used that to compare it).   
  
Maybe the curvature is such that it is missed? On this scale? But then, the next intersections are also missed. Because the segment q1 is part of a longer linestring, and none of the intersections are there...  
  
Here is another view:  
![image](https://user-images.githubusercontent.com/334849/190128609-21325253-b64d-4ca8-aa87-5a2edd7377e4.png)  
  
The full lines (parts) are:  
```  
P: LINESTRING(10.40012269074711 63.42843206062311,10.4029932904841 63.42811616062505)  
Q: LINESTRING(10.40062641940492 63.42837069900823,10.40062763824966 63.42837126010693,10.40062887877104 63.42837181159211,10.40063014059116 63.42837235329579,10.40063142332569 63.42837288505294,10.40063272658387 63.4283734067016,10.40063404996872 63.42837391808286,10.40063539307714 63.42837441904095,10.40063675549998 63.42837490942328,10.40063813682225 63.42837538908047,10.40063953662318 63.42837585786642,10.40064095447638 63.42837631563831)  
```  
  
One of the segments of this line should overlap...  
  
Picture including the linestrings P and Q  
![image](https://user-images.githubusercontent.com/334849/190132302-6f3f6e93-61f0-40fc-accd-c07eff8bef42.png)  
  
  
Can you investigate this?

---

## Comment 1

> Username: vissarion  
> Created at: 2022-09-14 11:28:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/1057#issuecomment-1246628000  

Sure, @barendgehrels I will have a look one of these days.

---

## Comment 2

> Username: vissarion  
> Created at: 2022-09-15 13:53:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/1057#issuecomment-1248134906  

I am testing the two segments p,q given above but I cannot reproduce your finding (with current develop branch). In particular, I am testing with a) spherical_equatorial CS, b) geographic with default (andoyer) formula policy and c) geographic with vincenty formula policy and I am getting the following intersection points respectively.  
```  
MULTIPOINT((10.400636755499979458 63.428374909423283157))  
MULTIPOINT((10.400638037144673831 63.428375354470347247))  
MULTIPOINT((10.400638034403748122 63.428375353516358359))  
```  
Cases (b) and (c) look correct while (a) not (the intersection point coincides with the southern point of segment q---red segment in 2nd figure above). Case (a) needs more investigation.   
  
Note that `intersects` return true for both spherical and geographic CS (AFAIU set operations do all computations in spherical).

---

## Comment 3

> Username: barendgehrels  
> Created at: 2022-09-15 18:40:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/1057#issuecomment-1248467107  

Okay, thanks for testing!  
  
I tested with geographic / andoyer.  
  
I'll look further next Wednesday. I'm about to publish a PR anyway. If I still have that problem, with that PR you might be able to find it.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2022-09-21 09:15:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/1057#issuecomment-1253431316  

Okay, apparently it happens, for this specific case, with `thomas` when using `long double` in the strategy calculations.  
  
I can reproduce it with this program (also adding some decimals to the coordinates)  
```  
#include <boost/geometry.hpp>  
  
namespace bg = boost::geometry;  
  
template <typename Strategy>  
void test()  
{  
    using coord_t = double;  
    using point_t = bg::model::point<coord_t, 2, bg::cs::geographic<bg::degree> >;  
    using segment_t = bg::model::referring_segment<point_t>;  
    using policy_t = bg::policies::relate::segments_intersection_policy  
        <  
            bg::segment_intersection_points<point_t, bg::segment_ratio<coord_t> >  
        >;  
  
    point_t p1, p2, q1, q2;  
    bg::read_wkt("POINT(10.400122690747114262421746389 63.4284320606231091232984908856)", p1);  
    bg::read_wkt("POINT(10.4029932904841029284170872415 63.428116160625052089017117396)", p2);  
    bg::read_wkt("POINT(10.4006367554999812341520737391 63.4283749094232831566841923632)", q1);  
    bg::read_wkt("POINT(10.4006381368222502459275347064 63.4283753890804717912033083849)", q2);  
  
    segment_t p(p1, p2);  
    segment_t q(q1, q2);  
  
    bg::detail::segment_as_subrange<segment_t> p_sr(p);  
    bg::detail::segment_as_subrange<segment_t> q_sr(q);  
  
    Strategy strategy;  
    std::cout << typeid(strategy).name() << std::endl;  
  
    auto result = strategy.apply(p_sr, q_sr, policy_t());  
  
    std::cout          
        << " p: " << bg::length(p)  
        << " q: " << bg::length(q)  
        << " int: " << result.intersection_points.count  
        << " how: " << result.direction.how  
        << " " << bg::wkt(result.intersection_points.intersections[0])  
        << std::endl;  
}  
  
  
int main(void)  
{  
    // Verify that the strategy is the same as the one where the error occured:  
    std::cout << "N5boost8geometry8strategy12intersection19geographic_segmentsINS1_6thomasELm2ENS0_3srs8spheroidIeEEeEE" << std::endl;  
  
    // The first two miss the intersection. The rest is OK  
    test<bg::strategy::intersection::geographic_segments<bg::strategy::thomas, 1, bg::srs::spheroid<long double>, long double>>();  
    test<bg::strategy::intersection::geographic_segments<bg::strategy::thomas, 2, bg::srs::spheroid<long double>, long double>>();  
    test<bg::strategy::intersection::geographic_segments<bg::strategy::thomas, 2, bg::srs::spheroid<double>, double>>();  
  
    test<bg::strategy::intersection::geographic_segments<bg::strategy::andoyer, 2, bg::srs::spheroid<long double>, long double>>();  
    test<bg::strategy::intersection::geographic_segments<bg::strategy::andoyer, 2, bg::srs::spheroid<double>, double>>();  
    return 0;  
}  
```

---

## Comment 5

> Username: barendgehrels  
> Created at: 2022-09-21 09:17:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/1057#issuecomment-1253432875  

This is the output, for me (it might be environment dependent):  
```  
N5boost8geometry8strategy12intersection19geographic_segmentsINS1_6thomasELm2ENS0_3srs8spheroidIeEEeEE  
N5boost8geometry8strategy12intersection19geographic_segmentsINS1_6thomasELm1ENS0_3srs8spheroidIeEEeEE  
 p: 147.588 q: 0.13477 int: 0 how: d POINT(2.10235e-317 6.9528e-310)  
N5boost8geometry8strategy12intersection19geographic_segmentsINS1_6thomasELm2ENS0_3srs8spheroidIeEEeEE  
 p: 147.588 q: 0.13477 int: 0 how: d POINT(2.10235e-317 6.9528e-310)  
N5boost8geometry8strategy12intersection19geographic_segmentsINS1_6thomasELm2ENS0_3srs8spheroidIdEEdEE  
 p: 147.588 q: 0.13477 int: 1 how: 0 POINT(10.4006 63.4284)  
N5boost8geometry8strategy12intersection19geographic_segmentsINS1_7andoyerELm2ENS0_3srs8spheroidIeEEeEE  
 p: 147.588 q: 0.13477 int: 1 how: i POINT(10.4006 63.4284)  
N5boost8geometry8strategy12intersection19geographic_segmentsINS1_7andoyerELm2ENS0_3srs8spheroidIdEEdEE  
 p: 147.588 q: 0.13477 int: 1 how: i POINT(10.4006 63.4284)  
```  
  
I will later today check why we use thomas here, and if andoyer would solve it. But I guess we might get errors in different places.

---

## Comment 6

> Username: barendgehrels  
> Created at: 2022-09-21 12:35:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/1057#issuecomment-1253646463  

The `thomas` was defined in the unit tests (by myself but long ago).  
I made it flexible now.  
Indeed, sometimes a turn is missed with `thomas`, sometimes with `andoyer`, both are rare cases but the result is a wrong buffer.  
I will now create the PR for buffer/line/geo

---

## Comment 7

> Username: barendgehrels  
> Created at: 2022-09-21 12:46:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/1057#issuecomment-1253659544  

https://github.com/boostorg/geometry/pull/1058

---

## Comment 8

> Username: vissarion  
> Created at: 2022-11-17 14:18:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/1057#issuecomment-1318707149  

This is related to geographic buffer so it goes to `1.82`.

---

## Comment 9

> Username: barendgehrels  
> Created at: 2023-01-11 12:58:15 UTC  
> Updated at: 2023-01-11 12:58:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/1057#issuecomment-1378710518  

I looked at in a bit more detail. It looks like that the underlying `sjoberg_intersection`, using `thomas`, calculates the wrong intersection point.  
  
With andoyer/vincenty:  
![image](https://user-images.githubusercontent.com/334849/211811940-7f9a64c5-7f13-48de-b3e3-70e422690fa5.png)  
  
  
With thomas:  
![image](https://user-images.githubusercontent.com/334849/211812024-aac93198-f275-40e2-9acf-2afcab2111d6.png)  
  
  
So the red intersection point is located wrongly. This is with coordinates in Trondheim. Far north. If we use coordinates in Greece, all goes as expected (as far as I can see).  
  
This is my new reproduction scenario.  
  
```  
#include <boost/geometry.hpp>  
  
#include <fstream>  
  
namespace bg = boost::geometry;  
  
template <typename Point, typename Line, typename Ip>  
void create_svg(std::string const& caseid, Line const& p, Line const& q, Ip const& ip, Ip const& ip1)  
{  
    std::ostringstream filename;  
    filename << "/tmp/svg/" << caseid << ".svg";  
    std::ofstream svg(filename.str());  
    bg::svg_mapper<Point> mapper(svg, 800, 800, 0.95);  
  
    // To show the bug, don't add p (very long) but q (very short).  
    // Lines intersect so they will both be visible.  
    // mapper.add(p);  
    mapper.add(q);  
    mapper.add(ip);  
    mapper.add(ip1);  
  
    mapper.map(p, "stroke:rgb(0,0,128);stroke-width:1.0");  
    mapper.map(q, "stroke:rgb(0,128,0);stroke-width:1.0");  
    for (const auto& pnt : p) {  
        mapper.map(pnt, "fill:rgb(0,128,128);stroke:rgb(0,0,0);stroke-width:0.5", 3);  
    }  
    for (const auto& pnt : q) {  
        mapper.map(pnt, "fill:rgb(0,128,0);stroke:rgb(0,0,0);stroke-width:0.5", 3);  
    }  
    mapper.map(ip, "fill:rgb(255,128,0);stroke:rgb(0,0,0);stroke-width:0.5", 4);  
    mapper.map(ip1, "fill:rgb(255,0,0);stroke:rgb(0,0,0);stroke-width:0.5", 4);  
}  
  
  
template <typename Point, typename FormulaPolicy>  
void segment_intersections(std::string const& caseid, std::string const& tag, std::string const& ps, std::string const& qs)  
{  
     using strategy_t = bg::strategy::intersection::geographic_segments<FormulaPolicy>;  
  
    using coord_t = typename bg::coordinate_type<Point>::type;  
    using linestring_t = bg::model::linestring<Point>;  
  
    // Lambda to get the intersection of two segments  
    auto get_intersection = [](const auto& pl, const auto& ql)  
    {  
        using result_type = bg::segment_intersection_points<Point>;  
        using policy_type = bg::policies::relate::segments_intersection_policy  
            <  
                result_type  
            >;  
        strategy_t strategy;  
        using segment_t = bg::model::referring_segment<const Point>;  
  
        segment_t seg_p(pl[0], pl[1]);  
        segment_t seg_q(ql[0], ql[1]);  
        bg::detail::segment_as_subrange<segment_t> const p_sub(seg_p);  
        bg::detail::segment_as_subrange<segment_t> const q_sub(seg_q);  
        return strategy.apply(p_sub, q_sub, policy_type());  
    };  
  
    // Lambda to get the side of a point w.r.t. two points  
    auto get_side = [](const auto& p0, const auto& p1, const auto& q)  
    {  
        using cs_tag = typename bg::cs_tag<Point>::type;  
        using strategy = typename bg::strategy::side::services::default_strategy<cs_tag, double>::type;  
        return strategy::apply(p0, p1, q);  
    };  
  
    // Lambda to translate side to L/R/- for Left,Right,collinear  
    auto ch = [](int sd) -> char  
    {  
        // "when"  
        return sd == -1 ? 'R'  
            : sd == 1 ? 'L'  
            : sd == 0 ? 'c'  
            : '?';  
    };  
  
    linestring_t pl, ql;  
    bg::read_wkt(ps, pl);  
    bg::read_wkt(qs, ql);  
  
    if (pl.size() < 2 || ql.size() < 2)  
    {  
        std::cerr << "LINE WITH 2 POINTS EXPECTED" << std::endl;  
        return;  
    }  
  
    // Get the intersection point (or two points), if any  
    const auto is = get_intersection(pl, ql);  
  
    auto const side_p_q0 = get_side(pl[0], pl[1], ql[0]);  
    auto const side_p_q1 = get_side(pl[0], pl[1], ql[1]);  
    auto const side_q_p0 = get_side(ql[0], ql[1], pl[0]);  
    auto const side_q_p1 = get_side(ql[0], ql[1], pl[1]);  
  
    if (is.intersection_points.count != 1)  
    {  
        std::cerr << "LINES DO NOT INTERSECT" << std::endl;  
        return;  
    }  
  
    if (side_p_q0 * side_p_q1 == 1)  
    {  
        // Both are on left or on right side.  
        // There should be no intersection.  
        // The sides should be equal (and non zero)  
        if (side_p_q0 != side_p_q1) { std::cerr << " ERROR"; }  
        if (is.intersection_points.count > 0) { std::cerr << " ERROR"; }  
        std::cerr << "LINES DO NOT CROSS" << std::endl;  
        return;  
    }  
    if (side_q_p0 * side_q_p1 == 1)  
    {  
        // Same for Q  
        if (side_q_p0 != side_q_p1) { std::cerr << " ERROR"; }  
        if (is.intersection_points.count > 0) { std::cerr << " ERROR"; }  
        std::cerr << "LINES DO NOT CROSS" << std::endl;  
        return;  
    }  
  
    Point recalculated_ip;  
    {  
        // Recalculate the intersection point in another way  
        // To show the underlying formula does it wrongly  
        auto const a1_lon = bg::get_as_radian<0>(pl[0]);  
        auto const a1_lat = bg::get_as_radian<1>(pl[0]);  
        auto const a2_lon = bg::get_as_radian<0>(pl[1]);  
        auto const a2_lat = bg::get_as_radian<1>(pl[1]);  
        auto const b1_lon = bg::get_as_radian<0>(ql[0]);  
        auto const b1_lat = bg::get_as_radian<1>(ql[0]);  
        auto const b2_lon = bg::get_as_radian<0>(ql[1]);  
        auto const b2_lat = bg::get_as_radian<1>(ql[1]);  
  
        using inverse_dist_azi = typename FormulaPolicy::template inverse<double, true, true, false, false, false>;  
  
        bg::srs::spheroid<double> spheroid;  
  
        auto const res_a1_a2 = inverse_dist_azi::apply(a1_lon, a1_lat, a2_lon, a2_lat, spheroid);  
        auto const res_b1_b2 = inverse_dist_azi::apply(b1_lon, b1_lat, b2_lon, b2_lat, spheroid);  
  
        double lon, lat;  
        bg::formula::sjoberg_intersection<double, FormulaPolicy::template inverse>  
            ::apply(a1_lon, a1_lat, a2_lon, a2_lat, res_a1_a2.azimuth,  
                    b1_lon, b1_lat, b2_lon, b2_lat, res_b1_b2.azimuth,  
                    lon, lat, spheroid);  
  
        // Print them. Coordinates are very close but not identical.                     
        std::cout << std::setprecision(16) << caseid << " " << tag << " "  
            << lon * 180.0 / M_PI << " " << lat  * 180.0 / M_PI   
            << " " << bg::get<0>(is.intersection_points.intersections[0])  
            << " " << bg::get<1>(is.intersection_points.intersections[0])  
            << std::endl;  
  
        bg::set_from_radian<0>(recalculated_ip, lon);  
        bg::set_from_radian<1>(recalculated_ip, lat);  
    }  
  
    std::ostringstream out;  
    out << caseid << "_" << tag << "_" << is.direction.how;  
    out << "_" << ch(side_p_q0) << ch(side_p_q1) << ch(side_q_p0) << ch(side_q_p1);  
  
    create_svg<Point>(out.str(), pl, ql, is.intersection_points.intersections[0], recalculated_ip);  
}  
  
int main(void)  
{  
    using coor_t = double;  
  
    {  
        // Coordinates from issue #1057  
        std::string ps = "LINESTRING(10.400122690632798594 63.428432060596314557,10.402993290369789037 63.428116160598243312)";  
        std::string qs = "LINESTRING(10.400636755547720824 63.428374909408333338,10.400638136870641759 63.428375389064719059)" ;  
        using point_t = bg::model::point<coor_t, 2, bg::cs::geographic<bg::degree> >;  
        segment_intersections<point_t, bg::strategy::andoyer>("bug", "andoyer", ps, qs);  
        segment_intersections<point_t, bg::strategy::thomas>("bug", "thomas", ps, qs);  
        segment_intersections<point_t, bg::strategy::vincenty>("bug", "vincenty", ps, qs);  
    }  
    {  
        // Coordinates in Greece  
        std::string ps = "LINESTRING(24.4346691 36.7428276, 24.4354761 36.7441070)";  
        std::string qs = "LINESTRING(24.4342816 36.7439902, 24.4363255 36.7429709)" ;  
        using point_t = bg::model::point<coor_t, 2, bg::cs::geographic<bg::degree> >;  
        segment_intersections<point_t, bg::strategy::andoyer>("milos", "andoyer", ps, qs);  
        segment_intersections<point_t, bg::strategy::thomas>("milos", "thomas", ps, qs);  
        segment_intersections<point_t, bg::strategy::vincenty>("milos", "vincenty", ps, qs);  
    }  
  
    return 0;  
}  
```

---

## Comment 10

> Username: barendgehrels  
> Created at: 2023-01-11 13:06:08 UTC  
> Updated at: 2023-01-11 13:06:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/1057#issuecomment-1378721484  

Also, thomas gives a `0` for `how` , so it is not marked as a regular intersection point (but a failure)  
That is because of these lines:  
```  
            res_a1_a2 = inverse_dist_azi::apply(a1_lon, a1_lat, a2_lon, a2_lat, spheroid);  
            if (math::equals(res_a1_a2.distance, c0))  
```  
  
(or similar). But it is not true, that result should not be zero because the coordinates are not colocated. For other formula's, this information is right.  
  
I first tried to work around that, but in the final result it does not make a difference. The problem is that the intersection point is quite off.

---

## Comment 11

> Username: vissarion  
> Created at: 2023-01-16 14:36:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/1057#issuecomment-1384157361  

Barend, thanks for the data and the computations!   
  
As you said the inaccuracy comes from thomas inverse formula, for the test case of the issue some intermediate results are becoming zero and then thomas return zero azimuth which in turn results in intersection point which is off.   
  
This can be overcome be relaxing the condition [here](https://github.com/boostorg/geometry/blame/fc67d2ef0e7aefb9f7b2088777ac2fced973042a/include/boost/geometry/formulas/thomas_inverse.hpp#L117) (although I do not know the effect of this on the test suite). By doing this the `recalculated_ip` is as in andoyer and vincenty cases but the other method find the segments disjoint (in particular [this](https://github.com/boostorg/geometry/blob/8e3785ed678bf733d00eed0141608e63a2371150/include/boost/geometry/strategies/geographic/intersection.hpp#L758) variable is `false` as opposed to `true` in andoyer and vincenty cases).   
  
The more general problem is that inverse formulas like andoyer and thomas are not stable with points that are too close. E.g. if you use these segments   
```  
std::string ps = "LINESTRING(10.400637816055676 63.4283753727334,10.400638245628214 63.4283753258664)";  
std::string qs = "LINESTRING(10.40063778986223 63.428375269625974,10.400638151331803 63.428375391480216)" ;  
```   
shorter versions of your Trondheim example, then both andoyer and thomas find them disjoint whereas vincenty computes the correct intersection.   
  
Should we only use vincenty for segments whose length is below some threshold?

---

## Comment 12

> Username: barendgehrels  
> Created at: 2023-01-18 13:17:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/1057#issuecomment-1387059722  

Hi Vissarion, thanks for your answer.  
  
So yes, apparently Thomas cannot cope with this and we should mention that in the documentation. I'm a bit less worried now, if it is only Thomas, and only in regions far north, and we've two good alternatives, things might work for users. It's not the default formula.  
  
We might also consider to deprecate it.  
  
We could make things tuneable in the way, indeed, to use another Formula if things are close together... However, that will make the architecture a bit clumsier.  
  
If we do that anyway, on the scale I'm working, cartesian would do as well. I didn't try but I bet that the cartesian intersection is indistinguishable from the Andoyer (or even Vincenty) one of segments are only a few meters length.  
We get those short segments in the context of the buffer, each rounded end or join will contain many very small segments especially if the buffer distance is low. Then also Andoyer would be at risk, if I understand you correctly. Which is the default, so it would be a bit worse. However, the buffer itself could, somehow, switch to cartesian for those cases (so only for intersection of segments which are "very close" or "very short")  
  
Just a brainstorm.

---

## Comment 13

> Username: barendgehrels  
> Created at: 2023-01-18 14:09:24 UTC  
> Updated at: 2023-01-18 14:10:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/1057#issuecomment-1387133230  

But... now I know where to look for, it's also easy to reproduce failing cases with `Andoyer`, unfortunately.  
  
Here a case in Guayaquil (near equator), using Vincenty  
![image](https://user-images.githubusercontent.com/334849/213191044-23847ef4-7f50-4406-8d3e-50dd767ef41d.png)  
  
  
And here the same case using Andoyer (a bit more zoomed in).  
![image](https://user-images.githubusercontent.com/334849/213191334-5db96d51-f5c1-4ff5-b26b-47a26dd94685.png)  
  
It cannot create the outer buffer, because two intersection points are missing, see next figure  
  
![image](https://user-images.githubusercontent.com/334849/213193070-98674bea-5aed-4fcf-b007-12f5afba0184.png)  
  
  
Where the orange points (Vincenty) are on their own, the red points (Andoyer) are missing and therefore we don't get the outer buffer, and neither we get one of the interior rings.  
  
Because this is on the equator, with Andoyer, I'm worried again and I think it should be fixed somehow.
