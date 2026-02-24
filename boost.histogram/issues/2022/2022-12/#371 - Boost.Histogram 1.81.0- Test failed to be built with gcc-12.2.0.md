# #371 - Boost.Histogram 1.81.0: Test failed to be built with gcc-12.2.0 [Closed]

> Username: sgn  
> Created at: 2022-12-16 15:17:37 UTC  
> Updated at: 2022-12-23 10:35:57 UTC  
> Closed at: 2022-12-22 23:58:32 UTC  
> Url: https://github.com/boostorg/histogram/issues/371  

Hello, the test of Boost.Histogram failed to be built with gcc-12.2.0 with this error message:  
```  
gcc.compile.c++ bin.v2/libs/histogram/test/deduction_guides_test.test/gcc-12/release/deduction_guides_test.o                                                                                    
In file included from ./boost/histogram/axis.hpp:23,                                                                                                                                            
                 from libs/histogram/test/deduction_guides_test.cpp:10:                                                                                                                         
./boost/histogram/axis/regular.hpp: In instantiation of 'class boost::histogram::axis::regular<int, boost::use_default, boost::use_default, boost::use_default>':                               
./boost/histogram/axis/regular.hpp:249:3:   required by substitution of 'template<class Value, class Transform, class MetaData, class Options> regular(unsigned int, Value, Value, typename bo  
ost::histogram::axis::metadata_base_t<MetaData>::metadata_type, typename std::conditional<std::is_same<Options, boost::use_default>::value, boost::histogram::axis::option::bitset<3>, Options  
>::type)-> boost::histogram::axis::regular<Value, Transform, MetaData, Options> [with Value = int; Transform = boost::use_default; MetaData = boost::use_default; Options = boost::use_default  
]'                                                                                                                                                                                              
libs/histogram/test/deduction_guides_test.cpp:35:5:   required from here  
./boost/histogram/axis/regular.hpp:205:62: error: static assertion failed: regular axis requires floating point type  
  205 |   static_assert(std::is_floating_point<internal_value_type>::value,  
      |                                                              ^~~~~  
./boost/histogram/axis/regular.hpp:205:62: note: 'std::integral_constant<bool, false>::value' evaluates to false  
```  
  
Presumably because of gcc doesn't know how to convert those `int` to `double`.  
Below patch can fix the test, but I'm not sure if it's your intention:  
  
```diff  
Index: boost-1.81.0/libs/histogram/test/deduction_guides_test.cpp  
===================================================================  
--- boost-1.81.0.orig/libs/histogram/test/deduction_guides_test.cpp  
+++ boost-1.81.0/libs/histogram/test/deduction_guides_test.cpp  
@@ -32,21 +32,21 @@ int main() {  
     using axis::regular;  
     BOOST_TEST_TRAIT_SAME(decltype(regular(1, 0.0, 1.0)),  
                           regular<double, tr::id, null_type>);  
-    BOOST_TEST_TRAIT_SAME(decltype(regular(1, 0, 1)), regular<double, tr::id, null_type>);  
+    BOOST_TEST_TRAIT_SAME(decltype(regular(1, 0.0, 1.0)), regular<double, tr::id, null_type>);  
     BOOST_TEST_TRAIT_SAME(decltype(regular(1, 0.0f, 1.0f)),  
                           regular<float, tr::id, null_type>);  
-    BOOST_TEST_TRAIT_SAME(decltype(regular(1, 0, 1, 0)), regular<double, tr::id, int>);  
+    BOOST_TEST_TRAIT_SAME(decltype(regular(1, 0.0, 1.0, 0)), regular<double, tr::id, int>);  
     BOOST_TEST_TRAIT_SAME(decltype(regular(1, 0.0f, 1.0f, "x")),  
                           regular<float, tr::id, std::string>);  
-    BOOST_TEST_TRAIT_SAME(decltype(regular(tr::sqrt(), 1, 0, 1)),  
+    BOOST_TEST_TRAIT_SAME(decltype(regular(tr::sqrt(), 1, 0.0, 1.0)),  
                           regular<double, tr::sqrt, null_type>);  
     BOOST_TEST_TRAIT_SAME(decltype(regular(tr::sqrt(), 1, 0.0f, 1.0f, "x")),  
                           regular<float, tr::sqrt, std::string>);  
-    BOOST_TEST_TRAIT_SAME(decltype(regular(tr::sqrt(), 1, 0, 1, 0)),  
+    BOOST_TEST_TRAIT_SAME(decltype(regular(tr::sqrt(), 1, 0.0, 1.0, 0)),  
                           regular<double, tr::sqrt, int>);  
-    BOOST_TEST_TRAIT_SAME(decltype(regular(1, 0, 1, "x", op::growth)),  
+    BOOST_TEST_TRAIT_SAME(decltype(regular(1, 0.0, 1.0, "x", op::growth)),  
                           regular<double, tr::id, std::string, op::growth_t>);  
-    BOOST_TEST_TRAIT_SAME(decltype(regular(tr::sqrt(), 1, 0, 1, "x", op::growth)),  
+    BOOST_TEST_TRAIT_SAME(decltype(regular(tr::sqrt(), 1, 0.0, 1.0, "x", op::growth)),  
                           regular<double, tr::sqrt, std::string, op::growth_t>);  
   }  
   
@@ -70,8 +70,6 @@ int main() {  
     BOOST_TEST_TRAIT_SAME(decltype(variable({-1, 0, 1}, "foo")),  
                           variable<double, std::string>);  
     BOOST_TEST_TRAIT_SAME(decltype(variable({-1, 1}, 0)), variable<double, int>);  
-    BOOST_TEST_TRAIT_SAME(decltype(variable({-1, 1}, "foo", op::underflow)),  
-                          variable<double, std::string, op::underflow_t>);  
   
     BOOST_TEST_TRAIT_SAME(decltype(variable(std::vector<int>{{-1, 1}})),  
                           variable<double, null_type>);  
@@ -81,8 +79,8 @@ int main() {  
                           variable<double, std::string>);  
     BOOST_TEST_TRAIT_SAME(decltype(variable(std::vector<int>{{-1, 1}}, 0)),  
                           variable<double, int>);  
-    BOOST_TEST_TRAIT_SAME(decltype(variable({-1, 1}, "foo", op::growth)),  
-                          variable<double, std::string, op::growth_t>);  
+    // BOOST_TEST_TRAIT_SAME(decltype(variable({-1, 1}, "foo", op::growth)),  
+    //                      variable<double, std::string, op::growth_t>);  
   }  
   
   {  
@@ -91,8 +89,8 @@ int main() {  
     BOOST_TEST_TRAIT_SAME(decltype(category{"x", "y"}), category<std::string, null_type>);  
     BOOST_TEST_TRAIT_SAME(decltype(category({1, 2}, "foo")), category<int, std::string>);  
     BOOST_TEST_TRAIT_SAME(decltype(category({1, 2}, 0)), category<int, int>);  
-    BOOST_TEST_TRAIT_SAME(decltype(category({1, 2}, "foo", op::growth)),  
-                          category<int, std::string, op::growth_t>);  
+    // BOOST_TEST_TRAIT_SAME(decltype(category({1, 2}, "foo", op::growth)),  
+    //                      category<int, std::string, op::growth_t>);  
   }  
   
   {  
@@ -102,28 +100,28 @@ int main() {  
   }  
   
   {  
-    auto h = histogram(axis::regular(3, -1, 1), axis::integer(0, 4));  
+    auto h = histogram(axis::regular(3, -1.0, 1.0), axis::integer(0, 4));  
     BOOST_TEST_TRAIT_SAME(decltype(h),  
                           histogram<std::tuple<axis::regular<double, tr::id, null_type>,  
                                                axis::integer<int, null_type>>>);  
-    BOOST_TEST_EQ(h.axis(0), axis::regular(3, -1, 1));  
+    BOOST_TEST_EQ(h.axis(0), axis::regular(3, -1.0, 1.0));  
     BOOST_TEST_EQ(h.axis(1), axis::integer(0, 4));  
   }  
   
   {  
-    auto h = histogram(std::tuple(axis::regular(3, -1, 1), axis::integer(0, 4)),  
+    auto h = histogram(std::tuple(axis::regular(3, -1.0, 1.0), axis::integer(0, 4)),  
                        weight_storage());  
     BOOST_TEST_TRAIT_SAME(decltype(h),  
                           histogram<std::tuple<axis::regular<double, tr::id, null_type>,  
                                                axis::integer<int, null_type>>,  
                                     weight_storage>);  
-    BOOST_TEST_EQ(h.axis(0), axis::regular(3, -1, 1));  
+    BOOST_TEST_EQ(h.axis(0), axis::regular(3, -1.0, 1.0));  
     BOOST_TEST_EQ(h.axis(1), axis::integer(0, 4));  
   }  
   
   {  
-    auto a0 = axis::regular(5, 0, 5);  
-    auto a1 = axis::regular(3, -1, 1);  
+    auto a0 = axis::regular(5, 0.0, 5.0);  
+    auto a1 = axis::regular(3, -1.0, 1.0);  
     auto axes = {a0, a1};  
     auto h = histogram(axes);  
     BOOST_TEST_TRAIT_SAME(  
@@ -134,8 +132,8 @@ int main() {  
   }  
   
   {  
-    auto a0 = axis::regular(5, 0, 5);  
-    auto a1 = axis::regular(3, -1, 1);  
+    auto a0 = axis::regular(5, 0.0, 5.0);  
+    auto a1 = axis::regular(3, -1.0, 1.0);  
     auto axes = {a0, a1};  
     auto h = histogram(axes, weight_storage());  
     BOOST_TEST_TRAIT_SAME(  
@@ -147,8 +145,8 @@ int main() {  
   }  
   
   {  
-    auto a0 = axis::regular(5, 0, 5);  
-    auto a1 = axis::regular(3, -1, 1);  
+    auto a0 = axis::regular(5, 0.0, 5.0);  
+    auto a1 = axis::regular(3, -1.0, 1.0);  
     auto axes = std::vector<decltype(a0)>{{a0, a1}};  
     auto h = histogram(axes);  
     BOOST_TEST_TRAIT_SAME(  
  
```

---

## Comment 1

> Username: HDembinski  
> Created at: 2022-12-21 13:08:46 UTC  
> Updated at: 2022-12-21 13:10:00 UTC  
> Url: https://github.com/boostorg/histogram/issues/371#issuecomment-1361293209  

Thanks for reporting this, the error is also detected here https://www.boost.org/development/tests/develop/developer/output/teeks99-dkr-dg12-17-boost-bin-v2-libs-histogram-test-deduction_guides_test-test-gcc-12~c++17-debug-debug-symbols-off.html  
  
Looks like the deduction guides do not work as intended in gcc. The intend is that using integers works. The deduction guides make sure that the correct template is instantiated. It must work without changing the tests, as it does on clang and msvc. It could be a compiler-bug in gcc (not the first one I found).

---

## Comment 2

> Username: HDembinski  
> Created at: 2022-12-21 13:40:33 UTC  
> Updated at: 2022-12-21 13:46:15 UTC  
> Url: https://github.com/boostorg/histogram/issues/371#issuecomment-1361325890  

Looks like a gcc bug. The deduction guides actually work fine, this test passes on gcc-12.2 https://godbolt.org/z/ranKc6zoa  
  
But the code fails to compile, when I uncomment the static_assert.  
  
```c++  
// Copyright 2018 Hans Dembinski  
//  
// Distributed under the Boost Software License, Version 1.0.  
// (See accompanying file LICENSE_1_0.txt  
// or copy at http://www.boost.org/LICENSE_1_0.txt)  
  
// Copyright 2015-2018 Hans Dembinski  
//  
// Distributed under the Boost Software License, Version 1.0.  
// (See accompanying file LICENSE_1_0.txt  
// or copy at http://www.boost.org/LICENSE_1_0.txt)  
  
#ifndef BOOST_HISTOGRAM_AXIS_REGULAR_HPP  
#define BOOST_HISTOGRAM_AXIS_REGULAR_HPP  
  
#include <boost/core/nvp.hpp>  
#include <boost/histogram/axis/iterator.hpp>  
#include <boost/histogram/axis/metadata_base.hpp>  
#include <boost/histogram/axis/option.hpp>  
#include <boost/histogram/detail/convert_integer.hpp>  
#include <boost/histogram/detail/replace_type.hpp>  
#include <boost/histogram/fwd.hpp>  
#include <boost/mp11/utility.hpp>  
#include <boost/throw_exception.hpp>  
#include <cassert>  
#include <cmath>  
#include <limits>  
#include <stdexcept>  
#include <string>  
#include <type_traits>  
#include <utility>  
  
namespace boost {  
namespace histogram {  
namespace axis {  
  
namespace transform {  
  
/// Identity transform for equidistant bins.  
struct id {};  
  
} // namespace transform  
  
template <class Value, class Transform, class MetaData, class Options>  
class regular : public iterator_mixin<regular<Value, Transform, MetaData, Options>>,  
                protected detail::replace_default<Transform, transform::id>,  
                public metadata_base_t<MetaData> {  
  // these must be private, so that they are not automatically inherited  
  using value_type = Value;  
  using transform_type = detail::replace_default<Transform, transform::id>;  
  using metadata_base = metadata_base_t<MetaData>;  
  using metadata_type = typename metadata_base::metadata_type;  
  using options_type =  
      detail::replace_default<Options, decltype(option::underflow | option::overflow)>;  
  
  // static_assert(std::is_floating_point<value_type>::value,  
  //              "regular axis requires floating point type");  
  
public:  
  regular(unsigned n, value_type start, value_type stop, metadata_type meta = {},  
          options_type options = {});  
  
private:  
  index_type size_{0};  
  value_type min_{0}, delta_{1};  
};  
  
template <class T>  
regular(unsigned, T, T)  
    -> regular<detail::convert_integer<T, double>, transform::id, null_type>;  
  
} // namespace axis  
} // namespace histogram  
} // namespace boost  
  
#endif  
  
  
#include <boost/core/lightweight_test.hpp>  
#include <boost/core/lightweight_test_trait.hpp>  
  
using namespace boost::histogram;  
namespace tr = axis::transform;  
namespace op = axis::option;  
  
int main() {  
  using axis::null_type;  
  
  {  
    using axis::regular;  
    BOOST_TEST_TRAIT_SAME(decltype(regular(1, 0, 1)), regular<double, tr::id, null_type>);  
  }  
  
  return boost::report_errors();  
}  
```

---

## Comment 3

> Username: HDembinski  
> Created at: 2022-12-21 14:01:42 UTC  
> Updated at: 2022-12-21 14:02:11 UTC  
> Url: https://github.com/boostorg/histogram/issues/371#issuecomment-1361348012  

This does not seem to be the only issue with this test, though. Needs further investigation.

---

## Comment 4

> Username: sgn  
> Created at: 2022-12-21 14:06:30 UTC  
> Url: https://github.com/boostorg/histogram/issues/371#issuecomment-1361354058  

Yes, there're another issue with floating point comparision. The test run into failure with i686 target, too.

---

## Comment 5

> Username: sgn  
> Created at: 2022-12-21 14:08:57 UTC  
> Url: https://github.com/boostorg/histogram/issues/371#issuecomment-1361357160  

For your reference, here is the test failure output:  
```  
testing.capture-output bin.v2/libs/histogram/test/detail_erf_inv_test.test/gcc-12/release/threading-multi/detail_erf_inv_test.run                                                             ====== BEGIN OUTPUT ======                                                                                                                                                                      
libs/histogram/test/detail_erf_inv_test.cpp(22): test 'std::erf(erf_inv(xi)) ~= xi' ('nan' ~= '0') failed in function 'int main()'                                                              
1 error detected.                                                                                                                                                                               
                                                                                                                                                                                                
EXIT STATUS: 1    
====== BEGIN OUTPUT ======                                                                                                                                                                      
libs/histogram/test/indexed_test.cpp(119): test 'x.density() == *x / (x.bin(0).width() * x.bin(2).width())' ('11.1111' == '11.1111') failed in function 'void run_density_tests(boost::mp11::m  
p_list<Tag, Coverage>) [with Tag = boost::histogram::dynamic_tag; Coverage = std::integral_constant<boost::histogram::coverage, boost::histogram::coverage::inner>]'                            
libs/histogram/test/indexed_test.cpp(119): test 'x.density() == *x / (x.bin(0).width() * x.bin(2).width())' ('11.1111' == '11.1111') failed in function 'void run_density_tests(boost::mp11::m  
p_list<Tag, Coverage>) [with Tag = boost::histogram::dynamic_tag; Coverage = std::integral_constant<boost::histogram::coverage, boost::histogram::coverage::inner>]'                            
libs/histogram/test/indexed_test.cpp(119): test 'x.density() == *x / (x.bin(0).width() * x.bin(2).width())' ('11.1111' == '11.1111') failed in function 'void run_density_tests(boost::mp11::m  
p_list<Tag, Coverage>) [with Tag = boost::histogram::dynamic_tag; Coverage = std::integral_constant<boost::histogram::coverage, boost::histogram::coverage::all>]'                              
libs/histogram/test/indexed_test.cpp(119): test 'x.density() == *x / (x.bin(0).width() * x.bin(2).width())' ('11.1111' == '11.1111') failed in function 'void run_density_tests(boost::mp11::m  
p_list<Tag, Coverage>) [with Tag = boost::histogram::dynamic_tag; Coverage = std::integral_constant<boost::histogram::coverage, boost::histogram::coverage::all>]'                              
libs/histogram/test/indexed_test.cpp(119): test 'x.density() == *x / (x.bin(0).width() * x.bin(2).width())' ('11.1111' == '11.1111') failed in function 'void run_density_tests(boost::mp11::mp_list<Tag, Coverage>) [with Tag = boost::histogram::dynamic_tag; Coverage = std::integral_constant<boost::histogram::coverage, boost::histogram::coverage::all>]'                              
libs/histogram/test/indexed_test.cpp(119): test 'x.density() == *x / (x.bin(0).width() * x.bin(2).width())' ('11.1111' == '11.1111') failed in function 'void run_density_tests(boost::mp11::m  
p_list<Tag, Coverage>) [with Tag = boost::histogram::dynamic_tag; Coverage = std::integral_constant<boost::histogram::coverage, boost::histogram::coverage::all>]'                              
6 errors detected.                                                                                                                                                                              
                                                                                                 
EXIT STATUS: 6                                                                                                                                                                                  
====== END OUTPUT ======  
====== BEGIN OUTPUT ======                                                                                                                                                                      
libs/histogram/test/detail_normal_test.cpp(35): test 'normal_ppf(p) ~= xi' ('nan' ~= '0') failed in function 'int main()'                                                                       
1 error detected.                                                                                                                                                                               
                                                                                                                                                                                                
EXIT STATUS: 1                                                                                                                                                                                  
====== END OUTPUT ======   
====== BEGIN OUTPUT ======                                                                                                                                                                      
libs/histogram/test/axis_regular_test.cpp(114): test 'a.index(10.0) == 1' ('0' == '1') failed in function 'int main()'                                                                          
libs/histogram/test/axis_regular_test.cpp(116): test 'a.index(100.0) == 2' ('1' == '2') failed in function 'int main()'                                                                         
2 errors detected.                                                                               
                                                                                                 
EXIT STATUS: 2                                                                                                                                                                                  
====== END OUTPUT ======

---

## Comment 6

> Username: HDembinski  
> Created at: 2022-12-22 11:18:06 UTC  
> Updated at: 2022-12-23 10:35:57 UTC  
> Url: https://github.com/boostorg/histogram/issues/371#issuecomment-1362718607  

Ok, thanks for the additional report, but I need to know how to reproduce this.  
  
What do you mean with "i686 target"? Please tell me how you call b2 on the command line and on what platform you are running the tests.  
  
There are no known failures regarding floating point comparisons:  
https://www.boost.org/development/tests/develop/developer/histogram.html  
  
Please open a new issue for the floating point bug, if it is indeed a bug in the library.
