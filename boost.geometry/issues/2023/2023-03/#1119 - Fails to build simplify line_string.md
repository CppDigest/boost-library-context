# #1119 - Fails to build simplify line_string [Closed]

> Username: MPcoreDev  
> Created at: 2023-03-13 14:54:47 UTC  
> Updated at: 2023-03-15 13:48:12 UTC  
> Closed at: 2023-03-15 13:48:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/1119  

With Boost 1.81.0, under MSVC 2019 with -std:c++20, I don't manage to build a simple simplify of line_string.  
Here is the code:  
  
`  
#include <catch2/catch_test_macros.hpp>  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
  
TEST_CASE( "Test simplify", "[geometry]" ) {  
  
    namespace bg = boost::geometry;  
  
    typedef bg::model::d2::point_xy<double> xy;  
    boost::geometry::model::linestring<xy> geomIn { {-1, -1}, {-1, 0}, {0, 0}, {0, -1} };  
    boost::geometry::model::linestring<xy> out;  
    boost::geometry::simplify(geomIn, out, 0.5);  
  
    std::cout << "Out: " << boost::geometry::wkt(out) << std::endl;  
  
    REQUIRE( out.size() > 0 );  
}  
`  
  
Here the code is a test case but I have the same build issue in my main program.  
  
The error is:  
`  
C:\Projects\git\smart-map\external\.boost\boost\geometry\algorithms\not_implemented.hpp:47: error: C2338: static_assert failed: 'This operation is not or not yet implemented.'  
C:\Projects\git\smart-map\external\.boost\boost/geometry/algorithms/not_implemented.hpp(47): error C2338: static_assert failed: 'This operation is not or not yet implemented.'  
C:\Projects\git\smart-map\external\.boost\boost/geometry/algorithms/not_implemented.hpp(61): note: see reference to class template instantiation 'boost::geometry::nyi::not_implemented_error<Tag1,Tag2,std::integral_constant<size_t,2>>' being compiled  
`  
  
I don't manage to get what I miss...  
  
Full msvc trace is in the attached file.  
[log_msvc_simplify.txt](https://github.com/boostorg/geometry/files/10959491/log_msvc_simplify.txt)  
  
Tried also with -std:c++17 with same result.

---

## Comment 1

> Username: MPcoreDev  
> Created at: 2023-03-15 13:48:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/1119#issuecomment-1470042472  

Seems to be an issue with my configuration.  
Refreshing everything from scratch and it worked...
