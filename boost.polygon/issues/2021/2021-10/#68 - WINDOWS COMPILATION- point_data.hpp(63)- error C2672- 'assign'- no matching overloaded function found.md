# #68 - WINDOWS COMPILATION: point_data.hpp(63): error C2672: 'assign': no matching overloaded function found [Open]

> Username: nmz787  
> Created at: 2021-10-11 20:18:00 UTC  
> Updated at: 2021-10-11 20:18:00 UTC  
> Url: https://github.com/boostorg/polygon/issues/68  

Goal is to, in Windows, compile a Python extension to the Boost Polygon library, which is already compiling and working fine in Linux. The Linux makefile I was given used `-std=c++0x` which I read means C++11 standard, which is only supported in MS VC++ 2019 and up, so that is the compiler I used (aka MSVC 14.2).  
  
* downloaded latest Boost release: 1.77.0  
  * unzip to `c:\boost_1_77_0`  
* open `x64 Native Tools Command Prompt for VS 2019`  
* `cd c:\boost_1_77_0 `  
* `bootstrap.bat`  
* `b2 -j4 toolset=msvc-14.2 address-model=64 architecture=x86 link=shared threading=multi runtime-link=shared --build-type=minimal stage --stagedir=stage/x64 --with-python`  
* `cd c:\myPyPackage\cpp`  
* compile Python extension:  
`cl /EHsc /std:c11 /MD /W4 -O2 /I"c:\boost_1_77_0" /I"c:\python36\include"  boost_polygon_python.cpp c:\boost_1_77_0\stage\x64\lib\boost_python36-vc142-mt-x64-1_77.lib c:\python36\libs\python36.lib`  
  * https://docs.microsoft.com/en-us/cpp/build/reference/md-mt-ld-use-run-time-library?view=msvc-160  
  
output:  
```  
Microsoft (R) C/C++ Optimizing Compiler Version 19.29.30136 for x64  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
boost_polygon_python.cpp  
c:\boost_1_77_0\boost/python/converter/builtin_converters.hpp(170): warning C4244: 'argument': conversion from 'const long double' to 'double', possible loss of data  
c:\boost_1_77_0\boost/python/converter/builtin_converters.hpp(174): warning C4244: 'argument': conversion from '_Ty' to 'double', possible loss of data  
        with  
        [  
            _Ty=long double  
        ]  
c:\boost_1_77_0\boost\polygon\polygon_90_set_data.hpp(801): warning C4267: 'argument': conversion from 'size_t' to 'int', possible loss of data  
c:\boost_1_77_0\boost\polygon\polygon_90_set_data.hpp(789): note: while compiling class template member function 'boost::polygon::polygon_90_set_data<int> &boost::polygon::polygon_90_set_data<int>::interact(const boost::polygon::polygon_90_set_data<int> &)'  
boost_polygon_python.cpp(275): note: see reference to function template instantiation 'boost::polygon::polygon_90_set_data<int> &boost::polygon::polygon_90_set_data<int>::interact(const boost::polygon::polygon_90_set_data<int> &)' being compiled  
boost_polygon_python.cpp(186): note: see reference to class template instantiation 'boost::polygon::polygon_90_set_data<int>' being compiled  
c:\boost_1_77_0\boost/python/object_call.hpp(18): warning C4459: declaration of 'self' hides global declaration  
c:\boost_1_77_0\boost/python/self.hpp(24): note: see declaration of 'boost::python::self_ns::self'  
boost_polygon_python.cpp(143): note: see reference to function template instantiation 'boost::python::api::object boost::python::api::object_operators<boost::python::api::object>::operator ()<int,int>(const A0 &,const A1 &) const' being compiled  
        with  
        [  
            A0=int,  
            A1=int  
        ]  
boost_polygon_python.cpp(142): note: see reference to function template instantiation 'boost::python::api::object boost::python::api::object_operators<boost::python::api::object>::operator ()<int,int>(const A0 &,const A1 &) const' being compiled  
        with  
        [  
            A0=int,  
            A1=int  
        ]  
c:\boost_1_77_0\boost\polygon\point_data.hpp(63): error C2672: 'assign': no matching overloaded function found  
c:\boost_1_77_0\boost\polygon\point_data.hpp(57): note: see reference to function template instantiation 'boost::polygon::point_data<int> &boost::polygon::point_data<int>::operator =<PointType>(const PointType &)' being compiled  
        with  
        [  
            PointType=boost::python::extract<boost::polygon::Point>  
        ]  
c:\boost_1_77_0\boost\polygon\point_data.hpp(57): note: see reference to function template instantiation 'boost::polygon::point_data<int> &boost::polygon::point_data<int>::operator =<PointType>(const PointType &)' being compiled  
        with  
        [  
            PointType=boost::python::extract<boost::polygon::Point>  
        ]  
boost_polygon_python.cpp(81): note: see reference to function template instantiation 'boost::polygon::point_data<int>::point_data<boost::python::extract<boost::polygon::Point>>(const PointType &)' being compiled  
        with  
        [  
            PointType=boost::python::extract<boost::polygon::Point>  
        ]  
boost_polygon_python.cpp(81): note: see reference to function template instantiation 'boost::polygon::point_data<int>::point_data<boost::python::extract<boost::polygon::Point>>(const PointType &)' being compiled  
        with  
        [  
            PointType=boost::python::extract<boost::polygon::Point>  
        ]  
c:\boost_1_77_0\boost\polygon\point_data.hpp(63): error C2893: Failed to specialize function template 'enable_if<gtl_and_3<boost::polygon::y_pt_assign,is_mutable_point_concept<geometry_concept<T>::type>::type,is_point_concept<geometry_concept<PointType2>::type>::type>::type,PointType1>::type &boost::polygon::assign(PointType1 &,const PointType2 &)'  
c:\boost_1_77_0\boost\polygon\point_concept.hpp(167): note: see declaration of 'boost::polygon::assign'  
c:\boost_1_77_0\boost\polygon\point_data.hpp(63): note: With the following template arguments:  
c:\boost_1_77_0\boost\polygon\point_data.hpp(63): note: 'PointType1=boost::polygon::point_data<int>'  
c:\boost_1_77_0\boost\polygon\point_data.hpp(63): note: 'PointType2=PointType'  
```  
  
  
  
and here's the Python extension source:  
```  
#define BOOST_ALL_DYN_LINK  
#define BOOST_THREAD_USE_DLL  
#define BOOST_DYN_LINK  
#define BOOST_PYTHON_SHARED_LIB  
#define BOOST_POLYGON_MSVC  
#include <boost/python.hpp>  
#include <boost/polygon/polygon.hpp>  
using namespace boost::python;  
using namespace boost::polygon::operators;  
  
#include <algorithm>  
#include <vector>  
  
namespace boost {  
   namespace polygon {  
      typedef point_data<int> Point;  
      typedef rectangle_data<int> Rectangle;  
      typedef polygon_90_set_data<int> PolygonSet;  
   }  
}  
  
/*  
 * Extractors (Extract C++ objects from Python objects)  
 */  
namespace boost {  
   namespace python {  
  
      template <>  
      struct extract<boost::polygon::Point> {  
         typedef boost::polygon::Point result_type;  
  
         extract(boost::python::object const& obj) : obj_(obj) {}  
  
         result_type operator()() const {  
            return get_point();  
         }  
  
         operator result_type() const {  
            return get_point();  
         }  
  
         bool check() const {  
            boost::python::object point_class =  
               boost::python::import("myPyPackage.geometry.point").attr("Point");  
            return ::PyObject_IsInstance(obj_.ptr(), point_class.ptr());  
         }  
  
      private:  
         result_type get_point() const {  
            int x = extract<int>(obj_.attr("xcoord"));  
            int y = extract<int>(obj_.attr("ycoord"));  
            return boost::polygon::Point(x, y);  
         }  
  
      private:  
         boost::python::object obj_;  
      };  
  
      template <>  
      struct extract<boost::polygon::Rectangle> {  
         typedef boost::polygon::Rectangle result_type;  
  
         extract(boost::python::object const& obj) : obj_(obj) {}  
  
         result_type operator()() const {  
            return get_rectangle();  
         }  
  
         operator result_type() const {  
            return get_rectangle();  
         }  
  
         bool check() const {  
            boost::python::object rectangle_class =  
               boost::python::import("myPyPackage.geometry.rectangle").attr("Rectangle");  
            return ::PyObject_IsInstance(obj_.ptr(), rectangle_class.ptr());  
         }  
  
      private:  
         result_type get_rectangle() const {  
           boost::polygon::Point lower_left = extract<boost::polygon::Point>(obj_.attr("lower_left"));  
           boost::polygon::Point upper_right = extract<boost::polygon::Point>(obj_.attr("upper_right"));  
            return boost::polygon::Rectangle(lower_left.x(), lower_left.y(),  
                                             upper_right.x(), upper_right.y());  
         }  
  
      private:  
         boost::python::object obj_;  
      };  
  
      template <>  
      struct extract<std::vector<boost::polygon::Rectangle>> {  
         typedef std::vector<boost::polygon::Rectangle> result_type;  
  
         extract(boost::python::object const& obj) : obj_(obj) {}  
  
         result_type operator()() const {  
            return get_polygons();  
         }  
  
         operator result_type() const {  
            return get_polygons();  
         }  
  
         bool check() const {  
            boost::python::extract<boost::python::list> list_extract(obj_);  
            return list_extract.check();  
         }  
  
      private:  
         result_type get_polygons() const {  
            boost::python::extract<boost::python::list> list_extract(obj_);  
            if (!list_extract.check()) {  
               throw std::runtime_error("Expecting a python list of polygons/rectangles");  
            }  
  
            std::vector<boost::polygon::Rectangle> polygons;  
  
            boost::python::list polygon_list = list_extract();  
            for (size_t i=0; i<(size_t)boost::python::len(polygon_list); ++i) {  
               boost::python::object py_polygon = polygon_list[i];  
               boost::python::extract<boost::polygon::Rectangle> extract_polygon(py_polygon);  
               if (!extract_polygon.check()) {  
                  throw std::runtime_error("Expecting only rectangle in the list");  
               }  
               boost::polygon::Rectangle value = extract_polygon();  
               polygons.push_back(std::move(value));  
            }  
            return std::move(polygons);  
         }  
  
      private:  
         boost::python::object obj_;  
      };  
  
   }}  
  
struct Boost_Rectangle_To_Python_Rectangle {  
   static PyObject* convert(boost::polygon::Rectangle const& rectangle) {  
      boost::python::object point_class =  
         boost::python::import("myPyPackage.geometry.point").attr("Point");  
      boost::python::object pt1 = point_class(rectangle.get(boost::polygon::HORIZONTAL).low(),  
                                              rectangle.get(boost::polygon::VERTICAL).low());  
      boost::python::object pt2 = point_class(rectangle.get(boost::polygon::HORIZONTAL).high(),  
                                              rectangle.get(boost::polygon::VERTICAL).high());  
      boost::python::object rectangle_class =  
         boost::python::import("myPyPackage.geometry.rectangle").attr("Rectangle");  
      return boost::python::incref(rectangle_class(pt1, pt2).ptr());  
   }  
};  
  
struct ManhattanPolygons {  
   ManhattanPolygons();  
  
   ManhattanPolygons(ManhattanPolygons const& other);  
  
   ManhattanPolygons& operator=(ManhattanPolygons const& rhs);  
  
   void insert(boost::python::object py_polygon);  
  
   void clear();  
  
   boost::python::list get_rectangles();  
  
   boost::python::object extent() const;  
  
   ManhattanPolygons intersect(ManhattanPolygons const& other) const;  
  
   ManhattanPolygons join(ManhattanPolygons const& other) const;  
  
   ManhattanPolygons difference(ManhattanPolygons const& other) const;  
  
   ManhattanPolygons symmetric_difference(ManhattanPolygons const& other) const;  
  
   ManhattanPolygons resize(int value) const;  
  
   ManhattanPolygons interact(ManhattanPolygons const& other) const;  
  
   ManhattanPolygons scale(double factor) const;  
  
   std::shared_ptr<boost::polygon::PolygonSet> pPolygons_;  
};  
  
ManhattanPolygons::ManhattanPolygons()  
   : pPolygons_() {  
   pPolygons_.reset(new boost::polygon::PolygonSet);  
}  
  
ManhattanPolygons::ManhattanPolygons(ManhattanPolygons const& other)  
   : pPolygons_() {  
   pPolygons_.reset(new boost::polygon::PolygonSet);  
   *pPolygons_ = *(other.pPolygons_);  
}  
  
ManhattanPolygons& ManhattanPolygons::operator=(ManhattanPolygons const& rhs) {  
   if (this != &rhs) {  
      *pPolygons_ = *(rhs.pPolygons_);  
   }  
   return *this;  
}  
  
void ManhattanPolygons::insert(boost::python::object py_polygon) {  
   boost::python::extract<std::vector<boost::polygon::Rectangle>> polygons_extract(py_polygon);  
   if (polygons_extract.check()) {  
      std::vector<boost::polygon::Rectangle> polygons = polygons_extract();  
      for (auto it=polygons.cbegin(); it!=polygons.cend(); ++it) {  
         pPolygons_->insert(*it);  
      }  
      return;  
   }  
   boost::python::extract<boost::polygon::Rectangle> polygon_extract(py_polygon);  
   if (!polygon_extract.check()) {  
      throw std::runtime_error("Expecting a python rectangle or a list of python rectangles");  
   }  
   boost::polygon::Rectangle polygon = polygon_extract();  
   pPolygons_->insert(polygon);  
}  
  
void ManhattanPolygons::clear() {  
   pPolygons_->clear();  
}  
  
boost::python::list ManhattanPolygons::get_rectangles() {  
   boost::python::list list_of_rectangles;  
   std::vector<boost::polygon::Rectangle> result;  
   pPolygons_->get_rectangles(result);  
   for (auto itr=result.begin(); itr!=result.end(); ++itr) {  
      boost::python::object python_rectangle(*itr);  
      list_of_rectangles.append(python_rectangle);  
   }  
   return list_of_rectangles;  
}  
  
boost::python::object ManhattanPolygons::extent() const {  
   boost::polygon::Rectangle rectangle;  
   if (pPolygons_->extents(rectangle)) {  
      boost::python::object py_rectangle(rectangle);  
      return py_rectangle;  
   }  
   return boost::python::object();  
}  
  
ManhattanPolygons ManhattanPolygons::intersect(ManhattanPolygons const& other) const {  
   ManhattanPolygons result(*this);  
   *(result.pPolygons_) &= *(other.pPolygons_);  
   return std::move(result);  
}  
  
ManhattanPolygons ManhattanPolygons::join(ManhattanPolygons const& other) const {  
   ManhattanPolygons result(*this);  
   *(result.pPolygons_) += *(other.pPolygons_);  
   return std::move(result);  
}  
  
ManhattanPolygons ManhattanPolygons::difference(ManhattanPolygons const& other) const {  
   ManhattanPolygons result(*this);  
   *(result.pPolygons_) -= *(other.pPolygons_);  
   return std::move(result);  
}  
  
ManhattanPolygons ManhattanPolygons::symmetric_difference(ManhattanPolygons const& other) const {  
   ManhattanPolygons result(*this);  
   *(result.pPolygons_) ^= *(other.pPolygons_);  
   return std::move(result);  
}  
  
ManhattanPolygons ManhattanPolygons::resize(int value) const {  
   ManhattanPolygons result(*this);  
   result.pPolygons_->resize(value, value, value, value);  
   return std::move(result);  
}  
  
ManhattanPolygons ManhattanPolygons::interact(ManhattanPolygons const& other) const {  
   ManhattanPolygons result(*this);  
   result.pPolygons_->interact(*(other.pPolygons_));  
   return std::move(result);  
}  
  
ManhattanPolygons ManhattanPolygons::scale(double factor) const {  
   ManhattanPolygons result(*this);  
   result.pPolygons_->scale(factor);  
   return std::move(result);  
}  
  
BOOST_PYTHON_MODULE(boost_polygon_python) {  
   boost::python::to_python_converter<boost::polygon::Rectangle,  
                                      Boost_Rectangle_To_Python_Rectangle>();  
  
   class_<ManhattanPolygons>("PolygonSet")  
      .def("insert", &ManhattanPolygons::insert)  
      .def("clear", &ManhattanPolygons::clear)  
      .def("get_rectangles", &ManhattanPolygons::get_rectangles)  
      .def("intersect", &ManhattanPolygons::intersect)  
      .def("join", &ManhattanPolygons::join)  
      .def("difference", &ManhattanPolygons::difference)  
      .def("symmetric_difference", &ManhattanPolygons::symmetric_difference)  
      .def("resize", &ManhattanPolygons::resize)  
      .def("interact", &ManhattanPolygons::interact)  
      .def("scale", &ManhattanPolygons::scale)  
      ;  
}  
```
