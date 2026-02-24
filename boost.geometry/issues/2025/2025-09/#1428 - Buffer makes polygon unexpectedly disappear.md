# #1428 - Buffer makes polygon unexpectedly disappear [Open]

> Username: gastald88  
> Created at: 2025-09-10 15:53:44 UTC  
> Updated at: 2026-01-01 19:00:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/1428  

After applying the buffer function of a positive amount on a polygon, it becomes empty.  
My platform toolset is Visual Studio 2019 (v142) and the Boost version is 1.89.0. Below a code to replicate the problem, let me know if further details are needed, thank you.  
  
```cpp  
#include <boost/geometry.hpp>  
#include <iostream>  
  
namespace bg = boost::geometry;  
using namespace bg::strategy::buffer;  
  
using point_t = bg::model::point<double, 2, bg::cs::cartesian>;  
using polygon_t = bg::model::polygon<point_t>;  
using GPolygon = bg::model::multi_polygon<polygon_t>;  
  
GPolygon apply_buffer(const GPolygon& poly, double amount)  
{  
    //buffer parameters  
    distance_symmetric<double> distance_strategy(amount);  
    end_round end_strategy;  
    point_circle circle_strategy(10);  
    side_straight side_strategy;  
    join_round join_strategy;  
  
    //apply buffering  
    GPolygon buffered_poly;  
    bg::buffer(poly, buffered_poly, distance_strategy, side_strategy,  
        join_strategy, end_strategy, circle_strategy);  
  
    return buffered_poly;  
}  
  
  
int main() {  
    std::cout << "Using Boost "  
        << BOOST_VERSION / 100000 << "."  // maj. version  
        << BOOST_VERSION / 100 % 1000 << "."  // min. version  
        << BOOST_VERSION % 100                // patch version  
        << std::endl;  
  
    //polygon to buffer  
    GPolygon poly;  
    bg::read_wkt("MULTIPOLYGON(((925.2271 -349.3228,925.2271 -323.3228,927.5271 -323.3228,927.5271 -294.3228,910.2271 -294.3228,910.2271 -226.6035,900.0271 -226.6035,900.0271 236.477,909.4271 245.877,900.0271 255.277,900.0271 1199.477,909.4271 1208.877,900.0271 1218.277,900.0271 1291.417,907.891 1299.281,908.05 1299.4881,908.1499 1299.7293,908.1839 1299.9881,908.1499 1300.2469,908.05 1300.4881,907.891 1300.6952,905.0271 1303.5591,905.0271 1314.5628,909.4271 1325.7903,909.4271 1343.277,911.9271 1343.277,911.9271 1349.277,925.0271 1349.277,925.0271 1340.777,925.0647 1340.4432,925.1756 1340.1262,925.3543 1339.8418,925.5918 1339.6043,925.8763 1339.4256,926.1933 1339.3146,926.5271 1339.277,926.8609 1339.3146,927.1779 1339.4256,927.4623 1339.6043,927.6998 1339.8418,927.8785 1340.1262,927.9895 1340.4432,928.0271 1340.777,928.0271 1349.277,1331.0271 1349.277,1331.0271 1340.777,1331.0647 1340.4432,1331.1756 1340.1262,1331.3543 1339.8418,1331.5918 1339.6043,1331.8763 1339.4256,1332.1933 1339.3146,1332.5271 1339.277,1332.8609 1339.3146,1333.1779 1339.4256,1333.4623 1339.6043,1333.6998 1339.8418,1333.8785 1340.1262,1333.9895 1340.4432,1334.0271 1340.777,1334.0271 1349.277,1347.1271 1349.277,1347.1271 1343.277,1349.6271 1343.277,1349.6271 1325.7903,1354.0271 1314.5628,1354.0271 1303.5591,1351.1631 1300.6952,1351.0042 1300.4881,1350.9043 1300.2469,1350.8702 1299.9881,1350.9043 1299.7293,1351.0042 1299.4881,1351.1631 1299.281,1359.0271 1291.417,1359.0271 1218.277,1349.6271 1208.877,1359.0271 1199.477,1359.0271 255.277,1349.6271 245.877,1359.0271 236.477,1359.0271 -226.6035,1348.8271 -226.6035,1348.8271 -294.3228,1331.5271 -294.3228,1331.5271 -323.3228,1333.8271 -323.3228,1333.8271 -349.3228,925.2271 -349.3228)))", poly);  
  
    std::cout << std::boolalpha  
        << "is polygon valid? " << bg::is_valid(poly)  
        << std::endl << std::endl;  
  
    double amount = 0.5;  
    GPolygon buffered_poly = apply_buffer(poly, amount);  
  
	std::cout << "buffering polygon of " << amount << " ..." << std::endl;  
  
    std::cout << "is polygon empty? " << std::boolalpha << boost::geometry::is_empty(buffered_poly) << std::endl;  
}  
```  
  
Output is:  
```  
Using Boost 1.89.0  
is polygon valid? true  
  
buffering polygon of 0.5 ...  
is polygon empty? true  
```

---

## Comment 1

> Username: tomjnixon  
> Created at: 2025-12-28 17:43:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/1428#issuecomment-3694915140  

Here's another example, which comes from investigating https://github.com/pcb2gcode/pcb2gcode/issues/688  
  
```cpp  
#include <string>  
#include <iostream>  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/geometries.hpp>  
  
const std::string in_wkt = "POLYGON((4.3682619587893887 -4.1310003149149068,4.3641732283464574 -4.1318897637795278,4.3433031640020534 -4.1318877158286345,4.3394152065649427 -4.1310003149149068,4.3361984830412972 -4.1286431167503697,4.3340601085274164 -4.1248202023311169,4.3336614173228352 -4.1122040157480315,4.3264912114934679 -4.1121661002100174,4.3228884312360663 -4.1108770048212797,4.3030530266430143 -4.0912555665042198,4.2988303313073022 -4.0925012585944236,4.2761583185209329 -4.0924195023807179,4.2714223444698947 -4.0903723571109065,4.2683726720370343 -4.0862106564945631,4.2677165354330713 -4.0826771653543314,4.2681152266376525 -4.0444711362515608,4.2699906444356364 -4.04095169005969,4.2731085442196592 -4.0384652479527299,4.2765568603670889 -4.0374527309758168,4.2821844803149602 -4.03740157480315,4.2821844803149602 -4.0137774796081613,4.2788396722285169 -4.0131935575788757,4.2757395597052623 -4.0113754879365571,4.2734988033780867 -4.0085656683759128,4.2724161564715448 -4.0051387235912808,4.2723425196850391 -3.9665354330708658,4.2734988033780867 -3.9619067725689687,4.2766959776871376 -3.9583656475417803,4.2807843027729007 -3.9567930960444788,4.2871057401574806 -3.9566929133858264,4.2871057401574806 -3.9311023622047245,4.283657245510204 -3.9308951115321236,4.2823183630863664 -3.9294406975128067,4.282218599155013 -3.9233154660672227,4.283657245510204 -3.9214670931922857,4.2996126460272448 -3.9212682468183888,4.3015735082460784 -3.9225493420446922,4.3015735082460784 -3.9298128626797171,4.3000134977537909 -3.9310270675835119,4.2969482598425195 -3.9311023622047245,4.2969482598425195 -3.9566929133858264,4.3032708153373349 -3.9567930960444788,4.3066749626579552 -3.9579454223440464,4.3099261955371908 -3.9608808618313338,4.3115818156829846 -3.9649362585013757,4.3115085054995808 -4.0059309796274807,4.310360289523631 -4.0089161156859197,4.3080067893883731 -4.0116321996305908,4.3052154458817187 -4.0131935575788757,4.3018695196850398 -4.0137795275590546,4.3018695196850398 -4.0381013249487827,4.3060471169824028 -4.0412656313732507,4.3079972293552267 -4.0460423787709239,4.3080708661417324 -4.0740023262805263,4.3305245241131747 -4.0964559842519686,4.3336614173228352 -4.0964559842519686,4.3336696082741675 -4.0862127107671107,4.3341810727333474 -4.0834583222386547,4.3364735989643517 -4.0797258105241774,4.3397837923087712 -4.0775017796257789,4.3433031640020534 -4.0767737014942007,4.3647752131970661 -4.0767900799882542,4.3678933730455602 -4.0775017796257789,4.3709167491630696 -4.0794447854758049,4.3730410321643944 -4.0823436639850641,4.3739829978591951 -4.0858119165005213,4.374007557080164 -4.1224487065557245,4.3724532828034572 -4.1273685119828309,4.3706187080112731 -4.1294857241570302,4.3682619587893887 -4.1310003149149068))";  
  
typedef double coordinate_type_fp;  
typedef boost::geometry::model::d2::point_xy<coordinate_type_fp> point_type_fp;  
typedef boost::geometry::model::polygon<point_type_fp> polygon_type_fp;  
typedef boost::geometry::model::multi_polygon<polygon_type_fp> multi_polygon_type_fp;  
  
namespace bg = boost::geometry;  
  
int main(int, char **) {  
  std::cout << "Using Boost "  
    << BOOST_VERSION / 100000 << "."  // maj. version  
    << BOOST_VERSION / 100 % 1000 << "."  // min. version  
    << BOOST_VERSION % 100                // patch version  
    << std::endl;  
  
  polygon_type_fp geom_in;  
  bg::read_wkt(in_wkt, geom_in);  
  
  {  
    std::string valid_msg;  
    (void)bg::is_valid(geom_in, valid_msg);  
    std::cerr << "in: " << valid_msg << "\n";  
  }  
  
  // boost::geometry::correct(geom_in);  
  
  double expand_by = 0.004921259842519684971;  
  auto const points_per_circle = 32;  
  
  multi_polygon_type_fp geom_out;  
  bg::buffer(multi_polygon_type_fp{geom_in}, geom_out,  
             bg::strategy::buffer::distance_symmetric<coordinate_type_fp>(expand_by),  
             bg::strategy::buffer::side_straight(),  
             bg::strategy::buffer::join_round(points_per_circle),  
             bg::strategy::buffer::end_round(points_per_circle),  
             bg::strategy::buffer::point_circle(points_per_circle));  
  
  {  
    std::string valid_msg;  
    (void)bg::is_valid(geom_out, valid_msg);  
    std::cerr << "out: " << valid_msg << "\n";  
  }  
  
  std::cout << bg::to_wkt(geom_out) << "\n";  
  
  return 0;  
}  
```  
  
This yields, on boost 1.90:  
  
```  
Using Boost 1.90.0  
in: Geometry is valid  
out: Geometry is valid  
MULTIPOLYGON()  
```  
  
In earlier boost versions, this produced invalid output; here's the before/after for boost 1.88:  
  
<img width="1123" height="794" alt="Image" src="https://github.com/user-attachments/assets/9715aaa6-4819-43e3-b5aa-d941a73fdd8e" />  
  
Boost 1.79-1.88 fails in this way, 1.89 and 1.90 have no output, and 1.77 has correct output.  
  
I re-ran the example from the OP with 1.90, and it still fails:  
  
```  
Using Boost 1.90.0  
is polygon valid? true  
  
buffering polygon of 0.5 ...  
is polygon empty? true  
```

---

## Comment 2

> Username: eyal0  
> Created at: 2026-01-01 19:00:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/1428#issuecomment-3704002273  

Okay, I can confirm the issue... But what can I do about it?  Boost fixed it in a newer version, you say, so reporting it to boost isn't going to help.  
  
I could add a call to `bg::is_valid` after each buffer operation.  It could be hidden behind a `--debug` flag so that it wouldn't slow down processing in the normal case.  
  
In the past, I've usually just recommended that users use geos.  
  
Let me know what you think that I should do?  
  
Thanks for all your efforts on this, I can tell that it was a lot!
