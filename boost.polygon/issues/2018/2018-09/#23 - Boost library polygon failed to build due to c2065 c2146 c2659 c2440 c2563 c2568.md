# #23 - Boost library polygon failed to build due to c2065 c2146 c2659 c2440 c2563 c2568 [Closed]

> Username: spacelg  
> Created at: 2018-09-26 08:02:43 UTC  
> Updated at: 2018-10-14 21:43:38 UTC  
> Closed at: 2018-10-14 21:43:38 UTC  
> Url: https://github.com/boostorg/polygon/issues/23  

Environment:  
 VS 2017 + Windows Server 2016  
  
Issue description:  
 We build and run test for Boost. And we found voronoi_builder_test.cpp failed to build due to error c2065: 'T': undeclared identifier  
c2146: syntax error: missing ';' before identifier 'x'  
c2659'=': function as left operand  
c2440: '<function-style-cast>': cannot convert from 'initializer list' to 'boost::polygon::point_data<int>' c2563: mismatch in formal parameter list  
c2568: '*': unable to resolve function overload  
Could you pleaes take a look? Thanks in advance.  
[log_x86_test_75.log](https://github.com/boostorg/polygon/files/2418693/log_x86_test_75.log)  
  
  
Error info:  
msvc.link ..\out\x86rel\boost\bin.v2\libs\polygon\test\polygon_rectangle_formation_test.test\msvc-14.1\release\threading-multi\polygon_rectangle_formation_test.exe  
compile-c-c++ ..\out\x86rel\boost\bin.v2\libs\polygon\test\voronoi_builder_test.test\msvc-14.1\release\threading-multi\voronoi_builder_test.obj  
voronoi_builder_test.cpp  
libs\polygon\test\voronoi_builder_test.cpp(361): error C2065: 'T': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(361): error C2146: syntax error: missing ';' before identifier 'x'  
libs\polygon\test\voronoi_builder_test.cpp(361): error C2659: '=': function as left operand  
libs\polygon\test\voronoi_builder_test.cpp(362): error C2065: 'T': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(362): error C2146: syntax error: missing ';' before identifier 'y'  
libs\polygon\test\voronoi_builder_test.cpp(362): error C2659: '=': function as left operand  
libs\polygon\test\voronoi_builder_test.cpp(363): error C2440: '<function-style-cast>': cannot convert from 'initializer list' to 'boost::polygon::point_data<int>'  
libs\polygon\test\voronoi_builder_test.cpp(363): note: No constructor could take the source type, or constructor overload resolution was ambiguous  
libs\polygon\test\voronoi_builder_test.cpp(364): error C2563: mismatch in formal parameter list  
libs\polygon\test\voronoi_builder_test.cpp(364): error C2568: '*': unable to resolve function overload  
libs\polygon\test\voronoi_builder_test.cpp(364): note: could be 'enable_if<gtl_and<boost::polygon::y_p_sx,is_mutable_point_concept<geometry_concept<T>::type>::type>::type,void>::type boost::polygon::x(PointType &,point_mutable_traits<PointType>::coordinate_type)'  
libs\polygon\test\voronoi_builder_test.cpp(364): note: or       'enable_if<gtl_and<boost::polygon::y_p_x,is_point_concept<geometry_concept<T>::type>::type>::type,point_coordinate_type<GeometryType>::type>::type boost::polygon::x(const PointType &)'  
libs\polygon\test\voronoi_builder_test.cpp(580): error C2065: 'T': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(580): error C2146: syntax error: missing ';' before identifier 'x1'  
libs\polygon\test\voronoi_builder_test.cpp(580): error C2065: 'x1': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(580): error C2659: '=': function as left operand  
libs\polygon\test\voronoi_builder_test.cpp(580): error C2065: 'x2': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(580): error C2065: 'y2': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(581): error C2065: 'x1': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(581): error C2065: 'x2': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(581): error C2065: 'y2': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(582): error C2065: 'x1': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(583): error C2659: '=': function as left operand  
libs\polygon\test\voronoi_builder_test.cpp(584): error C2065: 'x2': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(585): error C2065: 'y2': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(587): error C2065: 'x1': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(588): error C2065: 'x2': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(588): error C2065: 'y2': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(618): error C2065: 'T': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(618): error C2146: syntax error: missing ';' before identifier 'x1'  
libs\polygon\test\voronoi_builder_test.cpp(618): error C2065: 'x1': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(619): error C2065: 'T': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(619): error C2146: syntax error: missing ';' before identifier 'y1'  
libs\polygon\test\voronoi_builder_test.cpp(619): error C2659: '=': function as left operand  
libs\polygon\test\voronoi_builder_test.cpp(620): error C2065: 'T': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(620): error C2146: syntax error: missing ';' before identifier 'dx'  
libs\polygon\test\voronoi_builder_test.cpp(620): error C2065: 'dx': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(620): error C2065: 'dy': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(621): error C2065: 'dx': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(621): error C2065: 'dy': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(622): error C2065: 'dx': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(623): error C2065: 'dy': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(625): error C2065: 'T': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(625): error C2146: syntax error: missing ';' before identifier 'x2'  
libs\polygon\test\voronoi_builder_test.cpp(625): error C2065: 'x2': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(625): error C2065: 'x1': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(625): error C2065: 'dx': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(626): error C2065: 'T': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(626): error C2146: syntax error: missing ';' before identifier 'y2'  
libs\polygon\test\voronoi_builder_test.cpp(626): error C2065: 'y2': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(626): error C2065: 'dy': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(627): error C2065: 'x1': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(628): error C2065: 'x2': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(628): error C2065: 'y2': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(634): error C2065: 'T': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(634): error C2146: syntax error: missing ';' before identifier 'x1'  
libs\polygon\test\voronoi_builder_test.cpp(634): error C2065: 'x1': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(635): error C2065: 'T': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(635): error C2146: syntax error: missing ';' before identifier 'y1'  
libs\polygon\test\voronoi_builder_test.cpp(635): error C2659: '=': function as left operand  
libs\polygon\test\voronoi_builder_test.cpp(636): error C2065: 'T': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(636): error C2146: syntax error: missing ';' before identifier 'x2'  
libs\polygon\test\voronoi_builder_test.cpp(636): error C2065: 'x2': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(637): error C2065: 'T': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(637): error C2146: syntax error: missing ';' before identifier 'y2'  
libs\polygon\test\voronoi_builder_test.cpp(637): error C2065: 'y2': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(638): error C2065: 'x1': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(639): error C2065: 'x2': undeclared identifier  
libs\polygon\test\voronoi_builder_test.cpp(639): error C2065: 'y2': undeclared identifier

---

## Comment 1

> Username: QuellaZhang  
> Created at: 2018-10-08 06:28:55 UTC  
> Url: https://github.com/boostorg/polygon/issues/23#issuecomment-427732316  

Hello, any updates here? Could some one help to look at this? Thanks you in advanced!  
  
**Update repro steps:**  
  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2015 x86 command prompt and browse to D:\Boost\src  
3. .\bootstrap                                                                          
4. .\b2 headers variant=release --build-dir=..\out\x86rel address-model=32              
5. .\b2 variant=release --build-dir=..\out\x86rel address-model=32  
6. .\b2 -j4 variant=release --build-dir=..\out\x86rel libs\polygon\test

---

## Comment 2

> Username: asydorchuk  
> Created at: 2018-10-08 10:08:31 UTC  
> Url: https://github.com/boostorg/polygon/issues/23#issuecomment-427781614  

Seems like a regression from this change: https://github.com/boostorg/polygon/pull/22.  
I will have a look today.

---

## Comment 3

> Username: asydorchuk  
> Created at: 2018-10-14 10:23:56 UTC  
> Url: https://github.com/boostorg/polygon/issues/23#issuecomment-429613134  

Pushed the fix: https://github.com/boostorg/polygon/commit/3c9474304025b447aca7ede08b5c237ae8d986b2
