# #1009 - bg::model::point template set fails to compile with gcc 12.1, but not 11.2. [Closed]

> Username: ghost  
> Created at: 2022-05-29 13:22:45 UTC  
> Updated at: 2022-05-29 20:01:39 UTC  
> Closed at: 2022-05-29 15:36:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/1009  

Hi,  
  
Just a note before moving on gcc bugzilla.   
Compiling with the boost/geometry.hpp header, using gcc 12.1, I get the following issue:  
  
```  
[proc] Executing command: /snap/cmake/current/bin/cmake --build custom_path/build --config Debug --target path_sim --  
[build] [1/2  50% :: 50.754] Building CXX object CMakeFiles/path_sim.dir/src/files.cpp.o  
[build] FAILED: CMakeFiles/path_sim.dir/src/files.cpp.o   
[build] custom_path/g++  -Icustom_path/include -Icustom_path/boost -Icustom_path -Icustom_path/CGAL-5.4/include -g -g -O0 -std=gnu++20 -MD -MT CMakeFiles/path_sim.dir/src/files.cpp.o -MF CMakeFiles/path_sim.dir/src/files.cpp.o.d -o CMakeFiles/path_sim.dir/src/files.cpp.o -c custom_path/src/files.cpp  
[build] In file included from custom_path/boost/boost/geometry/geometries/geometries.hpp:29,  
[build]                  from custom_path/boost/boost/geometry/io/svg/svg_mapper.hpp:43,  
[build]                  from custom_path/boost/boost/geometry/geometry.hpp:129,  
[build]                  from custom_path/boost/boost/geometry.hpp:17,  
[build]                  from custom_path/src/files.cpp:3:  
[build] custom_path/boost/boost/geometry/geometries/point_xyz.hpp: In member function ‘void boost::geometry::model::d3::point_xyz<CoordinateType, CoordinateSystem>::x(const CoordinateType&)’:  
[build] custom_path/boost/boost/geometry/geometries/point_xyz.hpp:68:27: error: type/value mismatch at argument 1 in template parameter list for ‘template<class _Key, class _Compare, class _Alloc> class std::set’  
[build]    68 |     { this->template set<0>(v); }  
[build]       |                           ^  
[build] custom_path/boost/boost/geometry/geometries/point_xyz.hpp:68:27: note:   expected a type, got ‘0’  
[build] custom_path/boost/boost/geometry/geometries/point_xyz.hpp:68:27: error: template argument 2 is invalid  
[build] custom_path/boost/boost/geometry/geometries/point_xyz.hpp:68:27: error: template argument 3 is invalid  
[build] custom_path/boost/boost/geometry/geometries/point_xyz.hpp: In member function ‘void boost::geometry::model::d3::point_xyz<CoordinateType, CoordinateSystem>::y(const CoordinateType&)’:  
[build] custom_path/boost/boost/geometry/geometries/point_xyz.hpp:72:27: error: type/value mismatch at argument 1 in template parameter list for ‘template<class _Key, class _Compare, class _Alloc> class std::set’  
[build]    72 |     { this->template set<1>(v); }  
[build]       |                           ^  
[build] custom_path/boost/boost/geometry/geometries/point_xyz.hpp:72:27: note:   expected a type, got ‘1’  
[build] custom_path/boost/boost/geometry/geometries/point_xyz.hpp:72:27: error: template argument 2 is invalid  
[build] custom_path/boost/boost/geometry/geometries/point_xyz.hpp:72:27: error: template argument 3 is invalid  
[build] custom_path/boost/boost/geometry/geometries/point_xyz.hpp: In member function ‘void boost::geometry::model::d3::point_xyz<CoordinateType, CoordinateSystem>::z(const CoordinateType&)’:  
[build] custom_path/boost/boost/geometry/geometries/point_xyz.hpp:76:27: error: type/value mismatch at argument 1 in template parameter list for ‘template<class _Key, class _Compare, class _Alloc> class std::set’  
[build]    76 |     { this->template set<2>(v); }  
[build]       |                           ^  
[build] custom_path/boost/boost/geometry/geometries/point_xyz.hpp:76:27: note:   expected a type, got ‘2’  
[build] custom_path/boost/boost/geometry/geometries/point_xyz.hpp:76:27: error: template argument 2 is invalid  
[build] custom_path/boost/boost/geometry/geometries/point_xyz.hpp:76:27: error: template argument 3 is invalid  
[build] ninja: build stopped: subcommand failed.  
[build] Build finished with exit code 1  
```  
It works fine with gcc 11.2 though..  
  
Just include the boost/geometry.hpp header and see the compilation fail with gcc 12.1.  
  
Maybe you can touch something in the meantime?  
  
Thanks,

---

## Comment 1

> Username: mloskot  
> Created at: 2022-05-29 14:22:54 UTC  
> Updated at: 2022-05-29 18:37:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/1009#issuecomment-1140459534  

It works for me https://godbolt.org/z/sGKvTE8sK  
  
Unless, you are not telling everything (Boost.Geometry version or branch, ~compiler flags~ it's `-std=gnu++20`).

---

## Comment 2

> Username: ghost  
> Created at: 2022-05-29 15:36:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/1009#issuecomment-1140473154  

Hmm: I had to put the header on the first line and the issue disappeared... Strange, but that's fine..  
Same thing happened with modules..  
Ok.. thanks anyway I close it

---

## Comment 3

> Username: mloskot  
> Created at: 2022-05-29 18:36:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/1009#issuecomment-1140502672  

> Same thing happened with modules..  
  
 Three more sentences and still nothing on which version of Boost.Geometry you use.  ¯\_(ツ)_/¯

---

## Comment 4

> Username: ghost  
> Created at: 2022-05-29 18:40:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/1009#issuecomment-1140503209  

My bad! Coding too much.. tried with 1.78 and 1.79 👍

---

## Comment 5

> Username: mloskot  
> Created at: 2022-05-29 18:43:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/1009#issuecomment-1140503660  

AFAICT, neither GCC nor Boost is modules-ready, so I'd forget it.  
  
From recent discussions on the list:  
  
- [It [Boost stuff] compiles with msvc and nothing else (...) I'm afraid my conclusion is that modules are simply not yet ready for  
prime time.](https://lists.boost.org/Archives/boost/2022/05/252961.php)  
- [`import "boost/lib.hpp"`, it all seems to "just work" under MSVC](https://lists.boost.org/Archives/boost/2022/05/252961.php)  
- https://gcc.gnu.org/bugzilla/show_bug.cgi?id=105320

---

## Comment 6

> Username: ghost  
> Created at: 2022-05-29 19:33:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/1009#issuecomment-1140510699  

Yes you are right, but the issue didn't happen in a module. I was referring to the fact that when I tried modules, I had the same issue with ordering includes. A different order could lead to an ICE or a segfault. I will recheck them next year, no time for random errors right now. Bottom line: I wasn't clear, so please excuse me for that.
