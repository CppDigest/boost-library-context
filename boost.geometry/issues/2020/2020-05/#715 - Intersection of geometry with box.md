# #715 - Intersection of geometry with box [Open]

> Username: vissarion  
> Created at: 2020-05-20 13:40:39 UTC  
> Updated at: 2020-05-20 13:40:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/715  

There are a few issues related to missing implementations and incosistent use of boxes in intersection algorithm, in particular when computing intersections between a geometry and a box. Consider the following piece of code  
```  
    typedef bg::model::point  
    <  
        double, 2,  
        bg::cs::cartesian  
    > point;  
    typedef bg::model::box<point> box;  
    typedef bg::model::linestring<point> linestring;  
    typedef bg::model::polygon<point> polygon;  
      
    point p;  
    box b;  
    linestring l;  
    polygon poly;  
  
    boost::geometry::read_wkt("POINT(1 1)", p);  
    boost::geometry::read_wkt("BOX(0 1,100 2)", b);  
    boost::geometry::read_wkt("POLYGON((0 0,100 3,1 0,0 0))", poly);  
    boost::geometry::read_wkt("LINESTRING(0 0,100 3)", l);  
  
    std::deque<point> output;  
    boost::geometry::intersection(b, poly, output);  
  
    int i = 0;  
    BOOST_FOREACH(auto const& q, output)  
    {  
        std::cout << i++ << ": " << bg::wkt(q) << std::endl;  
    }  
```  
which correctly returns  
```  
0: POINT(66.6667 2)  
1: POINT(33.3333 1)  
2: POINT(67 2)  
3: POINT(34 1)  
```  
Then if we change coordinate system by replacing `bg::cs::cartesian` for example with `bg::cs::geographic<bg::degree>` then we get   
```  
0: POINT(71.6255 2.89116)  
1: POINT(28.3026 1.44534)  
2: POINT(72.306 2.87737)  
3: POINT(29.6806 1.45875)  
```  
i.e. the box is treated as a polygon. Also if we replace ` boost::geometry::intersection(b, poly, output);` with either `boost::geometry::intersection(b, p, output);` or `boost::geometry::intersection(b, l, output);` we get a compile error.
