# #1259 - Compilation Error with Boost.Geometry and NVCC (CUDA 12.4): '__T0' does not name a type [Closed]

> Username: MarekZydor  
> Created at: 2024-03-11 13:24:41 UTC  
> Updated at: 2024-03-25 18:26:54 UTC  
> Closed at: 2024-03-25 18:26:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/1259  

I'm encountering a compilation error when trying to compile a project using Boost.Geometry with NVCC, part of CUDA 12.4. The project compiles fine with GCC alone, but when NVCC is involved, it throws errors related to type naming in Boost.Geometry's *sectionalize.hpp*.  
  
This issue is not present when compiling with **CUDA versions 12.3 and below**  
  
### Environment:  
- **Compiler**: GCC 9.5.0, NVCC from CUDA 12.4  
- **Boost Version**: tested on 1.84.0 and 1.79.0  
- **Build System**: Makefile  
  
### Error Logs:  
  
```  
[ 33%] Building CUDA object CMakeFiles/testGeometry.dir/main.cu.o  
/home/marekz/boost-1.84.0/include/boost/geometry/algorithms/detail/sections/sectionalize.hpp: In function ‘void boost::geometry::detail::sectionalize::enlarge_sections(Sections&, const Strategy&)’:  
/home/marekz/boost-1.84.0/include/boost/geometry/algorithms/detail/sections/sectionalize.hpp:739:12: error: ‘__T0’ does not name a type; did you mean ‘__y0’?  
  739 |         using gt = decltype(section.bounding_box);  
      |            ^~~~  
      |            __y0  
/home/marekz/boost-1.84.0/include/boost/geometry/algorithms/detail/sections/sectionalize.hpp:740:38: error: ‘__T0’ was not declared in this scope; did you mean ‘__y0’?  
  740 |         using ct = typename geometry::coordinate_type<gt>::type;  
      |                                      ^~~~  
      |                                      __y0  
/home/marekz/boost-1.84.0/include/boost/geometry/algorithms/detail/sections/sectionalize.hpp:740:42: error: template argument 1 is invalid  
  740 |         using ct = typename geometry::coordinate_type<gt>::type;  
      |                                          ^  
/home/marekz/boost-1.84.0/include/boost/geometry/algorithms/detail/sections/sectionalize.hpp:741:14: error: ‘ct’ does not name a type; did you mean ‘cs’?  
  741 |         static ct const eps = math::scaled_epsilon<ct>(1000);  
      |              ^~  
      |              cs  
/home/marekz/boost-1.84.0/include/boost/geometry/algorithms/detail/sections/sectionalize.hpp:742:43: error: ‘eps’ was not declared in this scope  
  742 |         expand_by_epsilon(section.bounding_box, eps);  
      |                                           ^~~  
make[3]: *** [CMakeFiles/testGeometry.dir/build.make:76: CMakeFiles/testGeometry.dir/main.cu.o] Error 1  
make[2]: *** [CMakeFiles/Makefile2:83: CMakeFiles/testGeometry.dir/all] Error 2  
make[1]: *** [CMakeFiles/Makefile2:90: CMakeFiles/testGeometry.dir/rule] Error 2  
make: *** [Makefile:124: testGeometry] Error 2  
```  
  
To produce the problem I created a test program:  
CMakeLists.txt:  
  
```  
cmake_minimum_required(VERSION 3.22)  
project(testGeometry CXX)  
  
set(CMAKE_C_COMPILER "/usr/bin/gcc-9")  
set(CMAKE_CXX_COMPILER "/usr/bin/g++-9")  
set(CMAKE_CUDA_HOST_COMPILER "/usr/bin/g++-9")  
  
set(CMAKE_CXX_STANDARD 17)  
  
enable_language(CUDA)  
  
include(FindCUDAToolkit)  
  
set(CMAKE_CUDA_FLAGS "${CMAKE_CUDA_FLAGS} --std c++17")  
set(CMAKE_CUDA_FLAGS "${CMAKE_CUDA_FLAGS} --maxrregcount 32")  
set(CMAKE_CUDA_FLAGS "${CMAKE_CUDA_FLAGS} --expt-relaxed-constexpr")  
set(CMAKE_CUDA_SEPARABLE_COMPILATION ON)  
  
set(CMAKE_PREFIX_PATH "${CMAKE_PREFIX_PATH};/home/marekz/boost-1.84.0/lib/cmake")  
find_package(Boost REQUIRED)  
  
add_executable(${PROJECT_NAME} main.cu)  
  
target_link_libraries(${PROJECT_NAME}  
        PUBLIC  
        CUDA::cudart  
        Boost::headers  
        PRIVATE  
        CUDA::curand  
        CUDA::cusolver  
)  
```  
  
main.cu  
  
```  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
#include <iostream>  
  
int main()  
{  
    // Define the point type  
    using point_t = boost::geometry::model::d2::point_xy<double>;  
  
    // Define the polygon type  
    using polygon_t = boost::geometry::model::polygon<point_t>;  
  
    // Create a point  
    point_t point(5.0, 5.0);  
  
    // Create a polygon  
    polygon_t poly;  
    boost::geometry::append(poly.outer(), point_t(0.0, 0.0));  
    boost::geometry::append(poly.outer(), point_t(0.0, 10.0));  
    boost::geometry::append(poly.outer(), point_t(10.0, 10.0));  
    boost::geometry::append(poly.outer(), point_t(10.0, 0.0));  
    boost::geometry::append(poly.outer(), point_t(0.0, 0.0));  
  
    // Calculate the distance  
    double dist = boost::geometry::distance(point, poly);  
  
    std::cout << "Distance between the point and the polygon is: " << dist << std::endl;  
  
    return 0;  
}  
```

---

## Comment 1

> Username: barendgehrels  
> Created at: 2024-03-23 10:19:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/1259#issuecomment-2016439294  

I cannot explain the compiler error, but apparently the first `decltype` fails. In this case it can be replaced by using `typename` so that should fix it. Please report if it does not fix it.

---

## Comment 2

> Username: MarekZydor  
> Created at: 2024-03-25 09:58:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/1259#issuecomment-2017623425  

Yes, that fixed the issue. It can be closed.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2024-03-25 18:26:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/1259#issuecomment-2018641980  

Cool, thanks for confirming it.
