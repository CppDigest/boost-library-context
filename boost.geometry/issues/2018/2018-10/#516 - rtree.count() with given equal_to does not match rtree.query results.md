# #516 - rtree.count() with given equal_to does not match rtree.query results [Closed]

> Username: Magnati  
> Created at: 2018-10-16 09:21:14 UTC  
> Updated at: 2018-10-17 11:42:05 UTC  
> Closed at: 2018-10-17 11:38:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/516  

Hello team at boostorg/geometry.  
  
I encountered a (at least for me) counter intuitive behaviour regarding the checking for present values in a rtree.  
When an own `equal_to` function is provided it is used in `rtree.count()` as far as I understood the documentation. So if I define it with a tolerance (as is below `my_eual` and then used in `inserter_count()`) it should have the exact same behaviour like if I do a query for the nearest neighbour and check its distance with the same tolerance (`inserter_query()`).  
  
But it does not.  
  
My guess is, that it might has something to do with the rebalancing of the tree, because in my bigger program I checked that the `my_equal` function was not visited some time when it should during the call for `count()`.  
  
Or I'm missing something.  
  
Or `count()` is not even meant to function like this.  
  
Here a reproducing example   
  
- OS: Ubuntu 18.04 Bionic Beaver  
- clang: 6.0  
- boost: 1.68.0  
  
```c++  
  
#include <boost/geometry.hpp>  
#include <iostream>  
#include <random>  
  
namespace bg = boost::geometry;  
namespace bgi = boost::geometry::index;  
  
using Point = boost::geometry::model::point<double, 2, bg::cs::cartesian>;  
struct Node {};  
struct Graph { Node addNode() { return Node(); } };  
typedef std::pair<Point, Node> Pair;  
  
constexpr double pi = 3.1415926535897932384626433832795;  
constexpr double piover180 = pi / 180.0;  
constexpr double equatorRadiusKiloMeter = 6378.388;  
constexpr double equatorRadiusMeter = equatorRadiusKiloMeter * 1000.0;  
  
/**  
 * geoDistance for lonlat points in meter  
 */  
double geoDistance(Point const & p1, Point const & p2){  
    double dlon = p1.get<0>() - p2.get<0>();  
    double dlat = p1.get<1>() - p2.get<1>();  
      
    double a = std::pow(std::sin(dlat / 2),2) + std::cos(p1.get<1>()) * std::cos(p2.get<1>()) * std::pow(std::sin(dlon / 2),2);  
    double c = 2 * std::atan2(std::sqrt(a), std::sqrt(1 - a));  
      
    double distance = equatorRadiusMeter * c;  
    return distance;  
}  
  
struct my_equal {   
    using result_type = bool;   
	  
    bool operator() (Pair const& v1, Pair const& v2) const {   
        auto distance = geoDistance(v1.first, v2.first);  
        //std::cout << "my_equal was used: distance = " << distance << std::endl;  
        if(distance < 1.0)  
            return true;  
        return false;  
    }   
};   
  
using BoostRTree = bgi::rtree<Pair, bgi::quadratic<16>, bgi::indexable<Pair>, my_equal>;  
using BoostQueryResult = std::vector<Pair>;  
  
/**  
 * only insert if count (which should use my_equal) reports 0 entries for this point  
 */  
void inserter_count(BoostRTree & rtree, Pair & newPair){  
    if( rtree.count<Pair>(newPair) == 0 ){  
        rtree.insert(newPair);	  
    }  
}  
  
/**  
 * only insert if nearest neaighbour is more than 1 m apart   
 */  
void inserter_query(BoostRTree & rtree, Pair & newPair){  
    BoostQueryResult result_query;  
    rtree.query(bgi::nearest(newPair.first, 1), std::back_inserter(result_query));  
    if(not result_query.empty())  
    {     
        auto value = geoDistance(newPair.first, result_query[0].first);  
        if( value > 1.0 )              
            rtree.insert(newPair);	  
    } else {   
        rtree.insert(newPair);	  
    }  
}  
  
double rand_double(double low, double high)  
{  
    static std::default_random_engine re {};  
    re.seed(std::clock());  
    using Dist = std::uniform_real_distribution<double>;  
    static Dist uid {};  
    return uid(re, Dist::param_type{low,high});  
}  
  
int smallTest(){  
    // points are 0.03 mm apart  
    Point one = Point(10.876809999751799, 51.953910004385598);  
    Point two = Point(10.876810000185101, 51.953910004242601);  
    // further away  
    Point three = Point(10.886810000135101, 51.953910004444601);  
    Graph graph;  
    Node dummy1 = graph.addNode();  
    Node dummy2 = graph.addNode();  
    Node dummy3 = graph.addNode();  
    Pair pair1 = std::make_pair(one, dummy1);  
    Pair pair2 = std::make_pair(two, dummy2);  
    Pair pair3 = std::make_pair(three, dummy2);  
    BoostRTree rtree1 = BoostRTree();  
    BoostRTree rtree2 = BoostRTree();  
  
    inserter_count(rtree1, pair1);  
    inserter_count(rtree1, pair2);  
    inserter_count(rtree1, pair3);  
	  
    inserter_query(rtree2, pair1);  
    inserter_query(rtree2, pair2);  
    inserter_count(rtree2, pair3);	  
  
    if(rtree1.count<Pair>(pair1) != rtree2.count<Pair>(pair2))  
    {  
        std::cout << rtree1.count<Pair>(pair1) << " != " << rtree2.count<Pair>(pair2) << std::endl;  
        return 1;  
    }else{  
        std::cout << "In general it works thou." << std::endl;  
        std::cout << rtree1.count<Pair>(pair1) << " == " << rtree2.count<Pair>(pair2) << std::endl;  
    }  
  
    rtree1 = BoostRTree();  
    rtree2 = BoostRTree();  
  
    inserter_count(rtree1, pair1);  
    inserter_count(rtree1, pair3);  
    inserter_count(rtree1, pair2);  
  
    inserter_query(rtree2, pair1);  
    inserter_count(rtree2, pair3);  
    inserter_query(rtree2, pair2);	  
  
    if(rtree1.count<Pair>(pair1) != rtree2.count<Pair>(pair2))  
    {  
        std::cout << rtree1.count<Pair>(pair1) << " != " << rtree2.count<Pair>(pair2) << std::endl;  
        return 1;  
    }else{  
        std::cout << "In different order too." << std::endl;  
        std::cout << rtree1.count<Pair>(pair1) << " == " << rtree2.count<Pair>(pair2) << std::endl;  
    }  
    return 0;  
}  
  
int biggerTest()  
{     
    Graph graph;  
    BoostRTree rtree3 = BoostRTree();  
    BoostRTree rtree4 = BoostRTree();  
    double lower_bound = 0;  
    double upper_bound = 0.00001;  
      
    for(int i = 0; i < 10000; i++){  
        double a_random_double = rand_double(lower_bound, upper_bound);  
        double b_random_double = rand_double(lower_bound, upper_bound);  
        std::cout << "Point(" << a_random_double << " : " << b_random_double << ")" << std::endl;  
        Point point = Point(a_random_double, b_random_double);  
        Node dummy = graph.addNode();  
        Pair pair = Pair(point, dummy);  
  
        inserter_count(rtree3, pair);          
        inserter_query(rtree4, pair);  
  
        if(rtree3.count<Pair>(pair) != rtree4.count<Pair>(pair))  
        {  
            std::cout << rtree3.count<Pair>(pair) << " != " << rtree4.count<Pair>(pair) << std::endl;  
            std::cout << "i = " << i << std::endl;  
            return 1;  
        }  
    }  
    return 0;  
}  
  
int main()  
{  
    std::cout << "#######################################################################################" << std::endl;  
    std::cout << "Small test shows that my_equal is used in count, and both inserter should work the same" << std::endl;  
    int result = smallTest();  
  
    std::cout << "#############" << std::endl;  
    std::cout << "but they dont" << std::endl;  
    result += biggerTest();  
      
    return result;  
}  
  
```

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-10-16 21:04:35 UTC  
> Updated at: 2018-10-17 11:42:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/516#issuecomment-430400323  

`equal_to` is used to check for equality of values contained in the R-tree (while counting or removing values), not to perform a query. For example if you're storing `std::pair<Point, Id>` and you know that the Id is unique it is sufficient to compare Ids. By default both components of `std::pair` are compared with Points compared geometrically by calling `bg::equals()`.  
  
While calling `count` the R-tree is traversed in search for values equal to the one you passed (as defined by `equal_to`). However pruning capabilities of the R-tree are used while doing this which means that nodes are analyzed geometrically. In this case only the nodes containing the points are taken into account (tested using `covered_by(point_you_passed, node_bounding_box)`). The R-tree thinks that geometrically your values are Points. This is why some sub-trees may be ommitted which is desireable.  
  
While performing knn query the nodes closest to the passed geometry are analyzed and then values inside these nodes. The query stops when there is enough number of values collected and all nodes in the vicinity are further than the furthest of the collected values.  
  
By checking the distance in `equal_to` you are effectively changing the geometry of your value from Point to Circle of radius 1. So geometrically some parts of the R-tree considers your values as Points (while analyzing nodes) and other parts as Circles (while checking equality). I'd expect that the result of `count()` and knn query would be different in this case and that it would be different for R-tree containing small number of nodes vs great number of nodes.  
  
I'm not entirely sure what is your goal but if you want to build an index of circles you could either:  
- implement your own Circle representation and implement all required Boost.Geometry algorithms for this type (like `bg::intersects()`, `bg::distance()`).  
- try to use the `nsphere` from the extensions (https://github.com/boostorg/geometry/tree/develop/include/boost/geometry/extensions/nsphere). But I don't think spherical CS is supported.  
- calculate bounding boxes of these circles and store them in the R-tree. This would be the most straightforward solution.  
  
Btw, you calculate the distance using haversine formula. Why not simply use `bg::cs::spherical_equatorial<bg::degree>` instead of `bg::cs::cartesian`?  
  
Another thing is that AFAIU you want to insert a value only if there is no other value already in the R-tree in the vincinity of constant size of 1 deg. You could replace knn query with spatial query (`bgi::intersects(enlarged_bounding_box_of_point)` with box being the point enlarged 1 deg in every direction). Spatial queries typically are faster than knn queries. The R-tree would not try to search some distant node for the closest value already stored in the R-tree if the value was not found in the region of interest.  
  
If nothing above solves your issue or have no sense to you then describe your initial problem you want to solve using R-tree because it's possible it could be solved otherwise (see: https://en.wikipedia.org/wiki/XY_problem).

---

## Comment 2

> Username: Magnati  
> Created at: 2018-10-17 11:23:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/516#issuecomment-430590659  

Fantastic answer.   
Got me clarity why it does not work **and** directions to improve my working code.   
Thank you.   
  
The issue was that the point data is noisy and you understood completely right, that only one point shall be inserted.

---

## Comment 3

> Username: awulkiew  
> Created at: 2018-10-17 11:38:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/516#issuecomment-430594277  

I'm glad I helped. Closing.
