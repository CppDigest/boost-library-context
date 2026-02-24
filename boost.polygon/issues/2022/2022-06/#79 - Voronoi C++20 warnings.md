# #79 - Voronoi C++20 warnings [Open]

> Username: yoavmil  
> Created at: 2022-06-26 07:31:39 UTC  
> Updated at: 2022-06-26 13:55:34 UTC  
> Url: https://github.com/boostorg/polygon/issues/79  

Compiling the most simple Voronoi code, at Visual Studio 2022 (V143), with C++20, gives a lot warnings.  
Most warnings are about using operator '+' or '>' between float and enum.  
I'll be glad if this was solved somehow. I would be happy to do a PR, but I need some guidance, because not all warnings lead to the source.  
  
Heres the minimalistic code snippet:  
```C++  
#include <boost/polygon/voronoi.hpp>  
#include <boost/geometry/geometries/adapted/boost_polygon.hpp>  
  
using boost::polygon::voronoi_diagram;  
typedef voronoi_diagram<double> VD;  
typedef boost::polygon::point_data<int64_t> Point;  
void stub() {  
	VD vd;  
  
	std::vector<Point> points;  
	construct_voronoi(points.cbegin(), points.cend(), &vd);  
}  
```  
  
Heres the warnings:  
```c++  
Build started...  
1>------ Build started: Project: BoostWarnings, Configuration: Release x64 ------  
1>stub.cpp  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\voronoi.hpp(38,38): warning C4244: 'argument': conversion from '__int64' to 'const int', possible loss of data  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\voronoi.hpp(51): message : see reference to function template instantiation 'unsigned __int64 boost::polygon::insert<boost::polygon::point_data<int64_t>,VB>(const Point &,VB *)' being compiled  
1>        with  
1>        [  
1>            VB=boost::polygon::default_voronoi_builder,  
1>            Point=boost::polygon::point_data<int64_t>  
1>        ]  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\voronoi.hpp(101): message : see reference to function template instantiation 'void boost::polygon::insert<PointIterator,boost::polygon::default_voronoi_builder>(const PointIterator,const PointIterator,VB *)' being compiled  
1>        with  
1>        [  
1>            PointIterator=std::_Vector_const_iterator<std::_Vector_val<std::_Simple_types<Point>>>,  
1>            VB=boost::polygon::default_voronoi_builder  
1>        ]  
1>C:\Users\User\source\repos\MagnusEngine\MagnusLib\stub.cpp(11): message : see reference to function template instantiation 'void boost::polygon::construct_voronoi<std::_Vector_const_iterator<std::_Vector_val<std::_Simple_types<_Ty>>>,VD>(const PointIterator,const PointIterator,VD *)' being compiled  
1>        with  
1>        [  
1>            _Ty=Point,  
1>            PointIterator=std::_Vector_const_iterator<std::_Vector_val<std::_Simple_types<Point>>>,  
1>            VD=VD  
1>        ]  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\voronoi.hpp(38,28): warning C4244: 'argument': conversion from '__int64' to 'const int', possible loss of data  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(1417,42): warning C5055: operator '>': deprecated between enumerations and floating-point types  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(1332): message : while compiling class template member function 'void boost::polygon::detail::voronoi_predicates<CTT>::lazy_circle_formation_functor<Site,Circle>::sss(const boost::polygon::detail::site_event<int> &,const boost::polygon::detail::site_event<int> &,const boost::polygon::detail::site_event<int> &,boost::polygon::detail::circle_event<double> &)'  
1>        with  
1>        [  
1>            CTT=boost::polygon::detail::voronoi_ctype_traits<boost::polygon::detail::int32>,  
1>            Site=boost::polygon::detail::site_event<int>,  
1>            Circle=boost::polygon::detail::circle_event<double>  
1>        ]  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(1498): message : see reference to function template instantiation 'void boost::polygon::detail::voronoi_predicates<CTT>::lazy_circle_formation_functor<Site,Circle>::sss(const boost::polygon::detail::site_event<int> &,const boost::polygon::detail::site_event<int> &,const boost::polygon::detail::site_event<int> &,boost::polygon::detail::circle_event<double> &)' being compiled  
1>        with  
1>        [  
1>            CTT=boost::polygon::detail::voronoi_ctype_traits<boost::polygon::detail::int32>,  
1>            Site=boost::polygon::detail::site_event<int>,  
1>            Circle=boost::polygon::detail::circle_event<double>  
1>        ]  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(1526): message : see reference to class template instantiation 'boost::polygon::detail::voronoi_predicates<CTT>::lazy_circle_formation_functor<Site,Circle>' being compiled  
1>        with  
1>        [  
1>            CTT=boost::polygon::detail::voronoi_ctype_traits<boost::polygon::detail::int32>,  
1>            Site=boost::polygon::detail::site_event<int>,  
1>            Circle=boost::polygon::detail::circle_event<double>  
1>        ]  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\voronoi_builder.hpp(509): message : see reference to class template instantiation 'boost::polygon::detail::voronoi_predicates<CTT>::circle_formation_predicate<boost::polygon::detail::site_event<int>,boost::polygon::detail::circle_event<double>,boost::polygon::detail::voronoi_predicates<CTT>::circle_existence_predicate<Site>,boost::polygon::detail::voronoi_predicates<CTT>::lazy_circle_formation_functor<Site,Circle>>' being compiled  
1>        with  
1>        [  
1>            CTT=boost::polygon::detail::voronoi_ctype_traits<boost::polygon::detail::int32>,  
1>            Site=boost::polygon::detail::site_event<int>,  
1>            Circle=boost::polygon::detail::circle_event<double>  
1>        ]  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\voronoi.hpp(102): message : see reference to class template instantiation 'boost::polygon::voronoi_builder<boost::polygon::detail::int32,boost::polygon::detail::voronoi_ctype_traits<boost::polygon::detail::int32>,boost::polygon::detail::voronoi_predicates<CTT>>' being compiled  
1>        with  
1>        [  
1>            CTT=boost::polygon::detail::voronoi_ctype_traits<boost::polygon::detail::int32>  
1>        ]  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(1418,42): warning C5055: operator '>': deprecated between enumerations and floating-point types  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(1419,50): warning C5055: operator '>': deprecated between enumerations and floating-point types  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(1214,41): warning C5055: operator '>': deprecated between enumerations and floating-point types  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(1138): message : while compiling class template member function 'void boost::polygon::detail::voronoi_predicates<CTT>::lazy_circle_formation_functor<Site,Circle>::pss(const boost::polygon::detail::site_event<int> &,const boost::polygon::detail::site_event<int> &,const boost::polygon::detail::site_event<int> &,int,boost::polygon::detail::circle_event<double> &)'  
1>        with  
1>        [  
1>            CTT=boost::polygon::detail::voronoi_ctype_traits<boost::polygon::detail::int32>,  
1>            Site=boost::polygon::detail::site_event<int>,  
1>            Circle=boost::polygon::detail::circle_event<double>  
1>        ]  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(1472): message : see reference to function template instantiation 'void boost::polygon::detail::voronoi_predicates<CTT>::lazy_circle_formation_functor<Site,Circle>::pss(const boost::polygon::detail::site_event<int> &,const boost::polygon::detail::site_event<int> &,const boost::polygon::detail::site_event<int> &,int,boost::polygon::detail::circle_event<double> &)' being compiled  
1>        with  
1>        [  
1>            CTT=boost::polygon::detail::voronoi_ctype_traits<boost::polygon::detail::int32>,  
1>            Site=boost::polygon::detail::site_event<int>,  
1>            Circle=boost::polygon::detail::circle_event<double>  
1>        ]  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(1215,41): warning C5055: operator '>': deprecated between enumerations and floating-point types  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(1216,49): warning C5055: operator '>': deprecated between enumerations and floating-point types  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(1316,41): warning C5055: operator '>': deprecated between enumerations and floating-point types  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(1317,41): warning C5055: operator '>': deprecated between enumerations and floating-point types  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(1318,49): warning C5055: operator '>': deprecated between enumerations and floating-point types  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(1124,44): warning C5055: operator '>': deprecated between enumerations and floating-point types  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(1051): message : while compiling class template member function 'void boost::polygon::detail::voronoi_predicates<CTT>::lazy_circle_formation_functor<Site,Circle>::pps(const boost::polygon::detail::site_event<int> &,const boost::polygon::detail::site_event<int> &,const boost::polygon::detail::site_event<int> &,int,boost::polygon::detail::circle_event<double> &)'  
1>        with  
1>        [  
1>            CTT=boost::polygon::detail::voronoi_ctype_traits<boost::polygon::detail::int32>,  
1>            Site=boost::polygon::detail::site_event<int>,  
1>            Circle=boost::polygon::detail::circle_event<double>  
1>        ]  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(1460): message : see reference to function template instantiation 'void boost::polygon::detail::voronoi_predicates<CTT>::lazy_circle_formation_functor<Site,Circle>::pps(const boost::polygon::detail::site_event<int> &,const boost::polygon::detail::site_event<int> &,const boost::polygon::detail::site_event<int> &,int,boost::polygon::detail::circle_event<double> &)' being compiled  
1>        with  
1>        [  
1>            CTT=boost::polygon::detail::voronoi_ctype_traits<boost::polygon::detail::int32>,  
1>            Site=boost::polygon::detail::site_event<int>,  
1>            Circle=boost::polygon::detail::circle_event<double>  
1>        ]  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(1125,44): warning C5055: operator '>': deprecated between enumerations and floating-point types  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(1126,52): warning C5055: operator '>': deprecated between enumerations and floating-point types  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(1037,44): warning C5055: operator '>': deprecated between enumerations and floating-point types  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(998): message : while compiling class template member function 'void boost::polygon::detail::voronoi_predicates<CTT>::lazy_circle_formation_functor<Site,Circle>::ppp(const boost::polygon::detail::site_event<int> &,const boost::polygon::detail::site_event<int> &,const boost::polygon::detail::site_event<int> &,boost::polygon::detail::circle_event<double> &)'  
1>        with  
1>        [  
1>            CTT=boost::polygon::detail::voronoi_ctype_traits<boost::polygon::detail::int32>,  
1>            Site=boost::polygon::detail::site_event<int>,  
1>            Circle=boost::polygon::detail::circle_event<double>  
1>        ]  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(1455): message : see reference to function template instantiation 'void boost::polygon::detail::voronoi_predicates<CTT>::lazy_circle_formation_functor<Site,Circle>::ppp(const boost::polygon::detail::site_event<int> &,const boost::polygon::detail::site_event<int> &,const boost::polygon::detail::site_event<int> &,boost::polygon::detail::circle_event<double> &)' being compiled  
1>        with  
1>        [  
1>            CTT=boost::polygon::detail::voronoi_ctype_traits<boost::polygon::detail::int32>,  
1>            Site=boost::polygon::detail::site_event<int>,  
1>            Circle=boost::polygon::detail::circle_event<double>  
1>        ]  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(1038,44): warning C5055: operator '>': deprecated between enumerations and floating-point types  
1>C:\Users\User\source\repos\Sandbox\vcpkg\installed\x64-windows\include\boost\polygon\detail\voronoi_predicates.hpp(1039,52): warning C5055: operator '>': deprecated between enumerations and floating-point types  
1>Done building project "BoostWarnings.vcxproj".  
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========  
```
