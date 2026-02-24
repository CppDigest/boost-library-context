# #244 - Indexed iterator does not work on array storage [Closed]

> Username: henryiii  
> Created at: 2019-10-26 03:43:16 UTC  
> Updated at: 2019-10-30 08:27:56 UTC  
> Closed at: 2019-10-30 08:27:55 UTC  
> Url: https://github.com/boostorg/histogram/issues/244  

The following code fails on Wandbox with Boost 1.71 (and similar behavior has been seen in develop):  
  
  
```cpp  
#include <iostream>  
#include <cstdlib>  
#include <boost/histogram.hpp>  
  
int main()  
{  
    std::cout << "Hello, Wandbox!" << std::endl;  
      
    namespace bh = boost::histogram;  
      
    auto h = bh::make_histogram_with(std::array<double, 7>(), bh::axis::integer<>(0,5));  
    h(2); h(3); h(3);  
      
    for(auto&& ind : bh::indexed(h) )  
        std::cout << *ind << " ";  
}  
```  
  
The failure:  
  
```  
In file included from /opt/wandbox/boost-1.71.0/gcc-head/include/boost/histogram/algorithm/project.hpp:16,  
                 from /opt/wandbox/boost-1.71.0/gcc-head/include/boost/histogram.hpp:31,  
                 from prog.cc:4:  
/opt/wandbox/boost-1.71.0/gcc-head/include/boost/histogram/indexed.hpp: In instantiation of 'class boost::histogram::indexed_range<boost::histogram::histogram<std::tuple<boost::histogram::axis::integer<int, boost::use_default, boost::use_default> >, boost::histogram::storage_adaptor<std::array<double, 7> > > >':  
/opt/wandbox/boost-1.71.0/gcc-head/include/boost/histogram/indexed.hpp:343:63:   required from 'auto boost::histogram::indexed(Histogram&&, boost::histogram::coverage) [with Histogram = boost::histogram::histogram<std::tuple<boost::histogram::axis::integer<int, boost::use_default, boost::use_default> >, boost::histogram::storage_adaptor<std::array<double, 7> > >&]'  
prog.cc:15:35:   required from here  
/opt/wandbox/boost-1.71.0/gcc-head/include/boost/histogram/indexed.hpp:47:9: error: 'boost::histogram::indexed_range<boost::histogram::histogram<std::tuple<boost::histogram::axis::integer<int, boost::use_default, boost::use_default> >, boost::histogram::storage_adaptor<std::array<double, 7> > > >::value_iterator' {aka 'double*'} is not a class, struct, or union type  
   47 |   using value_reference = typename value_iterator::reference;  
      |         ^~~~~~~~~~~~~~~  
/opt/wandbox/boost-1.71.0/gcc-head/include/boost/histogram/indexed.hpp:48:9: error: 'boost::histogram::indexed_range<boost::histogram::histogram<std::tuple<boost::histogram::axis::integer<int, boost::use_default, boost::use_default> >, boost::histogram::storage_adaptor<std::array<double, 7> > > >::value_iterator' {aka 'double*'} is not a class, struct, or union type  
   48 |   using value_type = typename value_iterator::value_type;  
      |         ^~~~~~~~~~  
prog.cc: In function 'int main()':  
prog.cc:16:21: error: no match for 'operator*' (operand type is 'boost::histogram::indexed_range<boost::histogram::histogram<std::tuple<boost::histogram::axis::integer<int, boost::use_default, boost::use_default> >, boost::histogram::storage_adaptor<std::array<double, 7> > > >::accessor')  
   16 |        std::cout << *ind << " ";  
      |                     ^~~~  
In file included from /opt/wandbox/boost-1.71.0/gcc-head/include/boost/histogram/algorithm/project.hpp:15,  
                 from /opt/wandbox/boost-1.71.0/gcc-head/include/boost/histogram.hpp:31,  
                 from prog.cc:4:  
/opt/wandbox/boost-1.71.0/gcc-head/include/boost/histogram/histogram.hpp:393:6: note: candidate: 'template<class A1, class S1, class A2, class S2> auto boost::histogram::operator*(const boost::histogram::histogram<A1, S1>&, const boost::histogram::histogram<A2, S2>&)'  
  393 | auto operator*(const histogram<A1, S1>& a, const histogram<A2, S2>& b) {  
      |      ^~~~~~~~  
/opt/wandbox/boost-1.71.0/gcc-head/include/boost/histogram/histogram.hpp:393:6: note:   template argument deduction/substitution failed:  
prog.cc:16:22: note:   'boost::histogram::indexed_range<boost::histogram::histogram<std::tuple<boost::histogram::axis::integer<int, boost::use_default, boost::use_default> >, boost::histogram::storage_adaptor<std::array<double, 7> > > >::accessor' is not derived from 'const boost::histogram::histogram<A1, S1>'  
   16 |        std::cout << *ind << " ";  
      |                      ^~~  
In file included from /opt/wandbox/boost-1.71.0/gcc-head/include/boost/histogram/algorithm/project.hpp:15,  
                 from /opt/wandbox/boost-1.71.0/gcc-head/include/boost/histogram.hpp:31,  
                 from prog.cc:4:  
/opt/wandbox/boost-1.71.0/gcc-head/include/boost/histogram/histogram.hpp:423:6: note: candidate: 'template<class A, class S> auto boost::histogram::operator*(const boost::histogram::histogram<A1, S1>&, double)'  
  423 | auto operator*(const histogram<A, S>& h, double x) {  
      |      ^~~~~~~~  
/opt/wandbox/boost-1.71.0/gcc-head/include/boost/histogram/histogram.hpp:423:6: note:   template argument deduction/substitution failed:  
prog.cc:16:22: note:   'boost::histogram::indexed_range<boost::histogram::histogram<std::tuple<boost::histogram::axis::integer<int, boost::use_default, boost::use_default> >, boost::histogram::storage_adaptor<std::array<double, 7> > > >::accessor' is not derived from 'const boost::histogram::histogram<A1, S1>'  
   16 |        std::cout << *ind << " ";  
      |                      ^~~  
In file included from /opt/wandbox/boost-1.71.0/gcc-head/include/boost/histogram/algorithm/project.hpp:15,  
                 from /opt/wandbox/boost-1.71.0/gcc-head/include/boost/histogram.hpp:31,  
                 from prog.cc:4:  
/opt/wandbox/boost-1.71.0/gcc-head/include/boost/histogram/histogram.hpp:433:6: note: candidate: 'template<class A, class S> auto boost::histogram::operator*(double, const boost::histogram::histogram<A1, S1>&)'  
  433 | auto operator*(double x, const histogram<A, S>& h) {  
      |      ^~~~~~~~  
/opt/wandbox/boost-1.71.0/gcc-head/include/boost/histogram/histogram.hpp:433:6: note:   template argument deduction/substitution failed:  
prog.cc:16:22: note:   candidate expects 2 arguments, 1 provided  
   16 |        std::cout << *ind << " ";  
      |                      ^~~  
In file included from /opt/wandbox/boost-1.71.0/gcc-head/include/boost/histogram/axis.hpp:12,  
                 from /opt/wandbox/boost-1.71.0/gcc-head/include/boost/histogram.hpp:34,  
                 from prog.cc:4:  
/opt/wandbox/boost-1.71.0/gcc-head/include/boost/histogram/axis/regular.hpp:41:3: note: candidate: 'template<class T> T boost::histogram::detail::operator*(T&&, const boost::histogram::detail::one_unit&)'  
   41 | T operator*(T&& t, const one_unit&) {  
      |   ^~~~~~~~  
/opt/wandbox/boost-1.71.0/gcc-head/include/boost/histogram/axis/regular.hpp:41:3: note:   template argument deduction/substitution failed:  
prog.cc:16:22: note:   candidate expects 2 arguments, 1 provided  
   16 |        std::cout << *ind << " ";  
      |                      ^~~  
1  
```

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-10-26 09:59:20 UTC  
> Url: https://github.com/boostorg/histogram/issues/244#issuecomment-546588711  

Thank you for reporting this, will be fixed.

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-10-30 08:27:55 UTC  
> Url: https://github.com/boostorg/histogram/issues/244#issuecomment-547789143  

fixed in develop
