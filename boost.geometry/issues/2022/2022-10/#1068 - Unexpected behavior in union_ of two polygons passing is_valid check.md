# #1068 - Unexpected behavior in union_ of two polygons passing is_valid check [Closed]

> Username: imartins727  
> Created at: 2022-10-18 23:34:31 UTC  
> Updated at: 2022-10-19 21:02:15 UTC  
> Closed at: 2022-10-19 15:28:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/1068  

When using union_ with two overlapping polygons which are confirmed to be valid by the is_valid method, the polygon that is returned is equivalent to the smaller polygon. This smaller polygon is nearly entirely contained by the larger polygon aside from some nearly parallel edges that extend out of the bottom of the larger polygon.   
  
I am able to reproduce the problem consistently with the following code.  
  
  
```  
    #include <boost/geometry.hpp>  
    #include <boost/geometry/geometries/point_xy.hpp>  
    #include <boost/geometry/geometries/polygon.hpp>  
    typedef boost::geometry::model::d2::point_xy<double> boostPoint;  
    typedef boost::geometry::model::ring<boostPoint> ring;  
    typedef boost::geometry::model::polygon<boostPoint> polygon;  
  
    std::string testPoly1_wkt = "POLYGON((193.17399597167969 -0.00130770995747298,99.253303527832031 0.469309002161026,96.669197082519531 0,0 0.48438799381256104,-17.070600509643555 38.88800048828125,-17.194999694824219 63.295101165771484,-28.77079963684082 65.838798522949219,-45.051998138427734 113.67500305175781,-24.947900772094727 163.89100646972656,-28.77079963684082 164.73100280761719,-45.051898956298828 212.5679931640625,-44.567501068115234 309.23699951171875,10.934399604797363 308.95901489257812,31.05470085144043 317.90200805664062,55.461799621582031 318.02700805664062,58.005500793457031 329.60299682617188,105.84200286865234 345.88400268554688,143.98199462890625 345.6929931640625,146.43699645996094 351.82501220703125,153.44200134277344 353.09698486328125,156.97099304199219 353.07901000976562,152.19599914550781 367.10800170898438,248.86599731445312 366.62298583984375,248.53599548339844 367.59100341796875,345.20498657226562 367.10699462890625,345.25 367.10699462890625,345.22900390625 367.1669921875,441.89898681640625 366.68301391601562,538.5679931640625 366.197998046875,538.23797607421875 367.16799926757812,634.906982421875 366.68399047851562,634.74200439453125 367.1669921875,731.4119873046875 366.68301391601562,731.24700927734375 367.1669921875,827.916015625 366.68301391601562,924.58481771190498 366.1990061496594,924.2550048828125 367.16799926757812,1020.9199829101562 366.68399047851562,1117.5799560546875 366.20001220703125,1117.260009765625 367.14999389648438,1213.9300537109375 366.66598510742188,1310.5999755859375 366.18099975585938,1307.75 359.07998657226562,1349.43994140625 358.87100219726562,1454.260009765625 316.906005859375,1494.6199951171875 316.70401000976562,1494.1300048828125 220.03500366210938,1491.780029296875 214.16000366210938,1494.6199951171875 214.14599609375,1494.1300048828125 117.47599792480469,1449.52001953125 6.0423898696899414,1442.52001953125 4.7701997756958008,1371.0799560546875 33.369098663330078,1358.22998046875 1.2721199989318848,1351.22998046875 -6.3995001255534589e-05,1257.3199462890625 0.47050100564956665,1254.719970703125 -0.00063597899861633778,1160.800048828125 0.46998399496078491,1158.219970703125 0.00067172897979617119,1064.31005859375 0.47123301029205322,1061.7099609375 9.974539716495201e-05,967.802978515625 0.47066199779510498,965.208984375 -0.0004722379962913692,871.2979736328125 0.47009098529815674,868.7039794921875 -0.0010442199418321252,774.78399658203125 0.46957099437713623,772.20001220703125 0.00024450299679301679,678.28997802734375 0.47080600261688232,675.69500732421875 -0.00032748098601587117,581.78497314453125 0.47023499011993408,579.19097900390625 -0.00089946400839835405,485.27099609375 0.46971699595451355,482.68600463867188 0.00040824300958774984,388.7760009765625 0.47097098827362061,386.1820068359375 -0.00016373999824281782,292.27200317382812 0.47040000557899475,289.67800903320312 -0.00073572399560362101,195.76800537109375 0.46982699632644653,193.17399597167969 -0.00130770995747298))";  
    std::string testPoly2_wkt = "POLYGON((531.67207224599406 141.27107126943156,514.60206492177531 179.67507822743937,497.53108103505656 218.07807810536906,497.52809031240031 218.71407969228312,485.83106882802531 245.02908213368937,476.86308176747843 271.38008005849406,474.13108713857218 271.98008616200968,457.85008127919718 319.81608469716593,441.56806833974406 367.65208323232218,538.23805124990031 367.16807444325968,634.90705759755656 366.68406565419718,590.29408152333781 255.25007517568156,545.68208201161906 143.81507761708781,538.67707712880656 142.54307444325968,531.67207224599406 141.27107126943156))";  
    polygon testPoly1, testPoly2;  
    std::vector<polygon> unionOutput;  
    boost::geometry::read_wkt(testPoly1_wkt, testPoly1);  
    boost::geometry::read_wkt(testPoly2_wkt, testPoly2);  
    std::cout << "Shape 1 is valid " << boost::geometry::is_valid(testPoly1) << " and Shape 2 is valid " << boost::geometry::is_valid(testPoly2) << "\n";  
    boost::geometry::union_(testPoly1, testPoly2, unionOutput);  
    std::cout << "Union output is size " << unionOutput.size() << "\n";  
    for (int i = 0; i < unionOutput.size(); i++) {  
        std::cout << "Shape " << i << " has " << unionOutput[i].outer().size() << " points\n";  
        for (auto p : unionOutput[i].outer()) {  
            std::cout << p.x() << " " << p.y() << ", ";  
        }  
        std::cout << "\n";  
    }  
```  
  
See the results I obtain from this plotted below where testPoly1 is blue, testPoly2 is yellow and the resulting polygon is green.  
  
![result plot](https://i.imgur.com/tjaZCyx.png)  
  
  
I would expect the union to be roughly the same as testPoly1. I have observed that small translations of testPoly2 result in unions that behave as expected. Since I would not expect translations to impact the validity of the polygons used, I am led to believe that union_ is encountering some numerical representation problems. Any insight would be greatly appreciated!

---

## Comment 1

> Username: barendgehrels  
> Created at: 2022-10-19 12:09:25 UTC  
> Updated at: 2022-10-19 12:10:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/1068#issuecomment-1283909836  

Hi, thanks for the report.  
Which version do you use?  
  
It might be a wrong result of the version with rescaling (up to version 1.79).   
But that changed. If you use Boost 1.80, the result will be good.  
  
This is my code (slightly adapted from yours) and output.  
```  
    std::cout << std::setprecision(16)  
        << "Shape 1 valid=" << boost::geometry::is_valid(testPoly1) << " area=" << boost::geometry::area(testPoly1) << std::endl  
        << "Shape 2 valid=" << boost::geometry::is_valid(testPoly2) << " area=" << boost::geometry::area(testPoly2) << std::endl;  
  
    boost::geometry::union_(testPoly1, testPoly2, unionOutput);  
  
    for (const auto& poly : unionOutput)  
    {  
        std::cout << "Output area=" << boost::geometry::area(poly) << std::endl;  
    }  
  
```  
  
Output:  
```  
Shape 1 valid=1 area=543121.7804684598  
Shape 2 valid=1 area=23897.00051503794  
Output area=543215.3562644606  
```  
  
The output looks like the first polygon, plus a little bit, and that looks like a part of the second one (I didn't visualize it)

---

## Comment 2

> Username: barendgehrels  
> Created at: 2022-10-19 12:15:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/1068#issuecomment-1283917525  

I actually mean: up to 1.78  
Do you use a version 1.78 or lower?

---

## Comment 3

> Username: imartins727  
> Created at: 2022-10-19 14:27:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/1068#issuecomment-1284108278  

I am using 1.78. I will update and try again. Thank you!

---

## Comment 4

> Username: imartins727  
> Created at: 2022-10-19 15:28:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/1068#issuecomment-1284195271  

I have confirmed that both my test cast and the larger problem it was extracted from are now behaving as expected when using 1.80. Thank you very much for your help!

---

## Comment 5

> Username: barendgehrels  
> Created at: 2022-10-19 21:02:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/1068#issuecomment-1284570416  

Thanks for the quick check, good it's OK now
