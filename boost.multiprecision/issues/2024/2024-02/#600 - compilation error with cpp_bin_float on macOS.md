# #600 - compilation error with cpp_bin_float on macOS [Closed]

> Username: tcaduser  
> Created at: 2024-02-24 03:16:13 UTC  
> Updated at: 2024-02-24 19:47:16 UTC  
> Closed at: 2024-02-24 19:47:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/600  

Using tag `boost-1.82.0` on `macOS 14.3.1 (23D60)`,  with the 14.2 sdk  
  
```  
/opt/homebrew/Cellar/cmake/3.28.3/bin/cmake -S/Users/jsanchez/git/devsim -B/Users/jsanchez/git/devsim/osx_x86_64_release --check-build-system CMakeFiles/Makefile.cmake 0  
/opt/homebrew/Cellar/cmake/3.28.3/bin/cmake -E cmake_progress_start /Users/jsanchez/git/devsim/osx_x86_64_release/CMakeFiles /Users/jsanchez/git/devsim/osx_x86_64_release//CMakeFiles/progress.marks  
/Library/Developer/CommandLineTools/usr/bin/make  -f CMakeFiles/Makefile2 all  
/Library/Developer/CommandLineTools/usr/bin/make  -f src/Geometry/CMakeFiles/Geometry.dir/build.make src/Geometry/CMakeFiles/Geometry.dir/depend  
cd /Users/jsanchez/git/devsim/osx_x86_64_release && /opt/homebrew/Cellar/cmake/3.28.3/bin/cmake -E cmake_depends "Unix Makefiles" /Users/jsanchez/git/devsim /Users/jsanchez/git/devsim/src/Geometry /Users/jsanchez/git/devsim/osx_x86_64_release /Users/jsanchez/git/devsim/osx_x86_64_release/src/Geometry /Users/jsanchez/git/devsim/osx_x86_64_release/src/Geometry/CMakeFiles/Geometry.dir/DependInfo.cmake "--color="  
/Library/Developer/CommandLineTools/usr/bin/make  -f src/Geometry/CMakeFiles/Geometry.dir/build.make src/Geometry/CMakeFiles/Geometry.dir/build  
make[2]: Nothing to be done for `src/Geometry/CMakeFiles/Geometry.dir/build'.  
[  7%] Built target Geometry  
/Library/Developer/CommandLineTools/usr/bin/make  -f src/models/CMakeFiles/models.dir/build.make src/models/CMakeFiles/models.dir/depend  
cd /Users/jsanchez/git/devsim/osx_x86_64_release && /opt/homebrew/Cellar/cmake/3.28.3/bin/cmake -E cmake_depends "Unix Makefiles" /Users/jsanchez/git/devsim /Users/jsanchez/git/devsim/src/models /Users/jsanchez/git/devsim/osx_x86_64_release /Users/jsanchez/git/devsim/osx_x86_64_release/src/models /Users/jsanchez/git/devsim/osx_x86_64_release/src/models/CMakeFiles/models.dir/DependInfo.cmake "--color="  
/Library/Developer/CommandLineTools/usr/bin/make  -f src/models/CMakeFiles/models.dir/build.make src/models/CMakeFiles/models.dir/build  
make[2]: Nothing to be done for `src/models/CMakeFiles/models.dir/build'.  
[ 21%] Built target models  
/Library/Developer/CommandLineTools/usr/bin/make  -f src/math/CMakeFiles/math.dir/build.make src/math/CMakeFiles/math.dir/depend  
cd /Users/jsanchez/git/devsim/osx_x86_64_release && /opt/homebrew/Cellar/cmake/3.28.3/bin/cmake -E cmake_depends "Unix Makefiles" /Users/jsanchez/git/devsim /Users/jsanchez/git/devsim/src/math /Users/jsanchez/git/devsim/osx_x86_64_release /Users/jsanchez/git/devsim/osx_x86_64_release/src/math /Users/jsanchez/git/devsim/osx_x86_64_release/src/math/CMakeFiles/math.dir/DependInfo.cmake "--color="  
Dependencies file "src/math/CMakeFiles/math.dir/CompressedMatrix.cc.o.d" is newer than depends file "/Users/jsanchez/git/devsim/osx_x86_64_release/src/math/CMakeFiles/math.dir/compiler_depend.internal".  
Consolidate compiler generated dependencies of target math  
/Library/Developer/CommandLineTools/usr/bin/make  -f src/math/CMakeFiles/math.dir/build.make src/math/CMakeFiles/math.dir/build  
[ 21%] Building CXX object src/math/CMakeFiles/math.dir/CompressedMatrix.cc.o  
cd /Users/jsanchez/git/devsim/osx_x86_64_release/src/math && /usr/bin/g++ -DBOOST_MATH_STANDALONE -DBOOST_MP_STANDALONE -DDEVSIM_COPYRIGHT_YEAR=\"2009-2024\" -DDEVSIM_EXTENDED_PRECISION -DDEVSIM_VERSION_STRING=\"2.7.1\" -DEIGEN_MPL2_ONLY -DLOAD_MATHLIBS -DUSE_BLAS -DUSE_CPP_BIN_FLOAT -DUSE_EIGEN_EXTENDED -DUSE_ITERATIVE_SOLVER -DUSE_LAPACK -DUSE_MATERIALDB -DVTKWRITER -I/Users/jsanchez/git/devsim/external/boostorg/config/include -I/Users/jsanchez/git/devsim/external/boostorg/math/include -I/Users/jsanchez/git/devsim/external/boostorg/multiprecision/include -I/Users/jsanchez/git/devsim/src/math/../utility -I/Users/jsanchez/git/devsim/src/math/../Geometry -I/Users/jsanchez/git/devsim/src/math/../Data -I/Users/jsanchez/git/devsim/src/math/../Circuit/data -I/Users/jsanchez/git/devsim/src/math/../Equation -I/Users/jsanchez/git/devsim/src/math/../math -I/Users/jsanchez/git/devsim/src/math/../errorSystem -I/Users/jsanchez/git/devsim/src/math/../MathEval -I/Users/jsanchez/git/devsim/src/math/../common_api -I/Users/jsanchez/git/devsim/external/eigen -O3 -DNDEBUG -std=gnu++17 -arch "arm64" -isysroot /Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk -Wall -fvisibility=hidden -MD -MT src/math/CMakeFiles/math.dir/CompressedMatrix.cc.o -MF CMakeFiles/math.dir/CompressedMatrix.cc.o.d -o CMakeFiles/math.dir/CompressedMatrix.cc.o -c /Users/jsanchez/git/devsim/src/math/CompressedMatrix.cc  
In file included from /Users/jsanchez/git/devsim/src/math/CompressedMatrix.cc:8:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/CompressedMatrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/Matrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/dsMathTypes.hh:10:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/vector:307:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/__memory_resource/polymorphic_allocator.h:20:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/tuple:259:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/compare:145:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/__compare/compare_partial_order_fallback.h:13:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/__compare/partial_order.h:14:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/__compare/weak_order.h:14:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/__compare/strong_order.h:20:  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:607:12: error: no matching function for call to 'isinf'  
    return std::isinf(__lcpp_x);  
           ^~~~~~~~~~  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/complex:598:29: note: in instantiation of function template specialization 'std::__constexpr_isinf<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>' requested here  
        bool __z_inf = std::__constexpr_isinf(__a) || std::__constexpr_isinf(__b);  
                            ^  
/Users/jsanchez/git/devsim/src/math/CompressedMatrix.cc:509:21: note: in instantiation of function template specialization 'std::operator*<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>' requested here  
      y[r] += scale * v;  
                    ^  
/Users/jsanchez/git/devsim/src/math/CompressedMatrix.cc:588:5: note: in instantiation of function template specialization 'dsMath::(anonymous namespace)::ColScaleMultiply<std::complex<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>>' requested here  
    ColScaleMultiply(Cols, Rows, Vals, x, y);  
    ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/math.h:429:87: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'float' for 1st argument  
_LIBCPP_NODISCARD_EXT inline _LIBCPP_CONSTEXPR_SINCE_CXX23 _LIBCPP_HIDE_FROM_ABI bool isinf(float __x) _NOEXCEPT {  
                                                                                      ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/math.h:433:114: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'double' for 1st argument  
_LIBCPP_NODISCARD_EXT inline _LIBCPP_CONSTEXPR_SINCE_CXX23 _LIBCPP_HIDE_FROM_ABI _LIBCPP_PREFERRED_OVERLOAD bool isinf(double __x) _NOEXCEPT {  
                                                                                                                 ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/math.h:437:87: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'long double' for 1st argument  
_LIBCPP_NODISCARD_EXT inline _LIBCPP_CONSTEXPR_SINCE_CXX23 _LIBCPP_HIDE_FROM_ABI bool isinf(long double __x) _NOEXCEPT {  
                                                                                      ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/math.h:417:80: note: candidate template ignored: requirement 'std::is_arithmetic<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>::value' was not satisfied [with _A1 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>]  
_LIBCPP_NODISCARD_EXT _LIBCPP_CONSTEXPR_SINCE_CXX23 _LIBCPP_HIDE_FROM_ABI bool isinf(_A1 __x) _NOEXCEPT {  
                                                                               ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/math.h:424:5: note: candidate template ignored: requirement 'std::is_arithmetic<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>::value' was not satisfied [with _A1 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>]  
    isinf(_A1) _NOEXCEPT {  
    ^  
In file included from /Users/jsanchez/git/devsim/src/math/CompressedMatrix.cc:8:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/CompressedMatrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/Matrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/dsMathTypes.hh:10:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/vector:307:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/__memory_resource/polymorphic_allocator.h:20:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/tuple:259:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/compare:145:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/__compare/compare_partial_order_fallback.h:13:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/__compare/partial_order.h:14:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/__compare/weak_order.h:14:  
In file included from /Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/__compare/strong_order.h:20:  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:587:12: error: no matching function for call to 'isnan'  
    return std::isnan(__lcpp_x);  
           ^~~~~~~~~~  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/complex:601:19: note: in instantiation of function template specialization 'std::__constexpr_isnan<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>' requested here  
            (std::__constexpr_isnan(__a) && std::__constexpr_isnan(__b))  
                  ^  
/Users/jsanchez/git/devsim/src/math/CompressedMatrix.cc:509:21: note: in instantiation of function template specialization 'std::operator*<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>' requested here  
      y[r] += scale * v;  
                    ^  
/Users/jsanchez/git/devsim/src/math/CompressedMatrix.cc:588:5: note: in instantiation of function template specialization 'dsMath::(anonymous namespace)::ColScaleMultiply<std::complex<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>>' requested here  
    ColScaleMultiply(Cols, Rows, Vals, x, y);  
    ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/math.h:455:87: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'float' for 1st argument  
_LIBCPP_NODISCARD_EXT inline _LIBCPP_CONSTEXPR_SINCE_CXX23 _LIBCPP_HIDE_FROM_ABI bool isnan(float __x) _NOEXCEPT {  
                                                                                      ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/math.h:459:114: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'double' for 1st argument  
_LIBCPP_NODISCARD_EXT inline _LIBCPP_CONSTEXPR_SINCE_CXX23 _LIBCPP_HIDE_FROM_ABI _LIBCPP_PREFERRED_OVERLOAD bool isnan(double __x) _NOEXCEPT {  
                                                                                                                 ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/math.h:463:87: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'long double' for 1st argument  
_LIBCPP_NODISCARD_EXT inline _LIBCPP_CONSTEXPR_SINCE_CXX23 _LIBCPP_HIDE_FROM_ABI bool isnan(long double __x) _NOEXCEPT {  
                                                                                      ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/math.h:445:80: note: candidate template ignored: requirement 'std::is_floating_point<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>::value' was not satisfied [with _A1 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>]  
_LIBCPP_NODISCARD_EXT _LIBCPP_CONSTEXPR_SINCE_CXX23 _LIBCPP_HIDE_FROM_ABI bool isnan(_A1 __x) _NOEXCEPT {  
                                                                               ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/math.h:450:80: note: candidate template ignored: requirement 'std::is_integral<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>::value' was not satisfied [with _A1 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>]  
_LIBCPP_NODISCARD_EXT _LIBCPP_CONSTEXPR_SINCE_CXX23 _LIBCPP_HIDE_FROM_ABI bool isnan(_A1) _NOEXCEPT {  
                                                                               ^  
In file included from /Users/jsanchez/git/devsim/src/math/CompressedMatrix.cc:8:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/CompressedMatrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/Matrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/dsMathTypes.hh:11:  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/complex:637:19: error: no matching function for call to '__constexpr_copysign'  
            __a = std::__constexpr_copysign(std::__constexpr_isinf(__a) ? _Tp(1) : _Tp(0), __a);  
                  ^~~~~~~~~~~~~~~~~~~~~~~~~  
/Users/jsanchez/git/devsim/src/math/CompressedMatrix.cc:509:21: note: in instantiation of function template specialization 'std::operator*<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>' requested here  
      y[r] += scale * v;  
                    ^  
/Users/jsanchez/git/devsim/src/math/CompressedMatrix.cc:588:5: note: in instantiation of function template specialization 'dsMath::(anonymous namespace)::ColScaleMultiply<std::complex<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>>' requested here  
    ColScaleMultiply(Cols, Rows, Vals, x, y);  
    ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:630:54: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'float' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI float __constexpr_copysign(float __x, float __y) _NOEXCEPT {  
                                                     ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:634:55: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'double' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI double __constexpr_copysign(double __x, double __y) _NOEXCEPT {  
                                                      ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:639:1: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'long double' for 1st argument  
__constexpr_copysign(long double __x, long double __y) _NOEXCEPT {  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:647:5: note: candidate template ignored: requirement 'std::is_arithmetic<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>::value' was not satisfied [with _A1 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>, _A2 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>]  
    __constexpr_copysign(_A1 __x, _A2 __y) _NOEXCEPT {  
    ^  
In file included from /Users/jsanchez/git/devsim/src/math/CompressedMatrix.cc:8:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/CompressedMatrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/Matrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/dsMathTypes.hh:11:  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/complex:638:19: error: no matching function for call to '__constexpr_copysign'  
            __b = std::__constexpr_copysign(std::__constexpr_isinf(__b) ? _Tp(1) : _Tp(0), __b);  
                  ^~~~~~~~~~~~~~~~~~~~~~~~~  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:630:54: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'float' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI float __constexpr_copysign(float __x, float __y) _NOEXCEPT {  
                                                     ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:634:55: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'double' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI double __constexpr_copysign(double __x, double __y) _NOEXCEPT {  
                                                      ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:639:1: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'long double' for 1st argument  
__constexpr_copysign(long double __x, long double __y) _NOEXCEPT {  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:647:5: note: candidate template ignored: requirement 'std::is_arithmetic<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>::value' was not satisfied [with _A1 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>, _A2 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>]  
    __constexpr_copysign(_A1 __x, _A2 __y) _NOEXCEPT {  
    ^  
In file included from /Users/jsanchez/git/devsim/src/math/CompressedMatrix.cc:8:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/CompressedMatrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/Matrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/dsMathTypes.hh:11:  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/complex:640:23: error: no matching function for call to '__constexpr_copysign'  
                __c = std::__constexpr_copysign(_Tp(0), __c);  
                      ^~~~~~~~~~~~~~~~~~~~~~~~~  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:630:54: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'float' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI float __constexpr_copysign(float __x, float __y) _NOEXCEPT {  
                                                     ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:634:55: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'double' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI double __constexpr_copysign(double __x, double __y) _NOEXCEPT {  
                                                      ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:639:1: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'long double' for 1st argument  
__constexpr_copysign(long double __x, long double __y) _NOEXCEPT {  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:647:5: note: candidate template ignored: requirement 'std::is_arithmetic<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>::value' was not satisfied [with _A1 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>, _A2 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>]  
    __constexpr_copysign(_A1 __x, _A2 __y) _NOEXCEPT {  
    ^  
In file included from /Users/jsanchez/git/devsim/src/math/CompressedMatrix.cc:8:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/CompressedMatrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/Matrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/dsMathTypes.hh:11:  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/complex:642:23: error: no matching function for call to '__constexpr_copysign'  
                __d = std::__constexpr_copysign(_Tp(0), __d);  
                      ^~~~~~~~~~~~~~~~~~~~~~~~~  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:630:54: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'float' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI float __constexpr_copysign(float __x, float __y) _NOEXCEPT {  
                                                     ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:634:55: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'double' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI double __constexpr_copysign(double __x, double __y) _NOEXCEPT {  
                                                      ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:639:1: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'long double' for 1st argument  
__constexpr_copysign(long double __x, long double __y) _NOEXCEPT {  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:647:5: note: candidate template ignored: requirement 'std::is_arithmetic<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>::value' was not satisfied [with _A1 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>, _A2 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>]  
    __constexpr_copysign(_A1 __x, _A2 __y) _NOEXCEPT {  
    ^  
In file included from /Users/jsanchez/git/devsim/src/math/CompressedMatrix.cc:8:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/CompressedMatrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/Matrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/dsMathTypes.hh:11:  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/complex:647:19: error: no matching function for call to '__constexpr_copysign'  
            __c = std::__constexpr_copysign(std::__constexpr_isinf(__c) ? _Tp(1) : _Tp(0), __c);  
                  ^~~~~~~~~~~~~~~~~~~~~~~~~  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:630:54: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'float' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI float __constexpr_copysign(float __x, float __y) _NOEXCEPT {  
                                                     ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:634:55: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'double' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI double __constexpr_copysign(double __x, double __y) _NOEXCEPT {  
                                                      ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:639:1: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'long double' for 1st argument  
__constexpr_copysign(long double __x, long double __y) _NOEXCEPT {  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:647:5: note: candidate template ignored: requirement 'std::is_arithmetic<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>::value' was not satisfied [with _A1 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>, _A2 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>]  
    __constexpr_copysign(_A1 __x, _A2 __y) _NOEXCEPT {  
    ^  
In file included from /Users/jsanchez/git/devsim/src/math/CompressedMatrix.cc:8:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/CompressedMatrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/Matrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/dsMathTypes.hh:11:  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/complex:648:19: error: no matching function for call to '__constexpr_copysign'  
            __d = std::__constexpr_copysign(std::__constexpr_isinf(__d) ? _Tp(1) : _Tp(0), __d);  
                  ^~~~~~~~~~~~~~~~~~~~~~~~~  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:630:54: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'float' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI float __constexpr_copysign(float __x, float __y) _NOEXCEPT {  
                                                     ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:634:55: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'double' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI double __constexpr_copysign(double __x, double __y) _NOEXCEPT {  
                                                      ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:639:1: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'long double' for 1st argument  
__constexpr_copysign(long double __x, long double __y) _NOEXCEPT {  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:647:5: note: candidate template ignored: requirement 'std::is_arithmetic<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>::value' was not satisfied [with _A1 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>, _A2 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>]  
    __constexpr_copysign(_A1 __x, _A2 __y) _NOEXCEPT {  
    ^  
In file included from /Users/jsanchez/git/devsim/src/math/CompressedMatrix.cc:8:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/CompressedMatrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/Matrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/dsMathTypes.hh:11:  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/complex:650:23: error: no matching function for call to '__constexpr_copysign'  
                __a = std::__constexpr_copysign(_Tp(0), __a);  
                      ^~~~~~~~~~~~~~~~~~~~~~~~~  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:630:54: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'float' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI float __constexpr_copysign(float __x, float __y) _NOEXCEPT {  
                                                     ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:634:55: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'double' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI double __constexpr_copysign(double __x, double __y) _NOEXCEPT {  
                                                      ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:639:1: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'long double' for 1st argument  
__constexpr_copysign(long double __x, long double __y) _NOEXCEPT {  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:647:5: note: candidate template ignored: requirement 'std::is_arithmetic<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>::value' was not satisfied [with _A1 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>, _A2 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>]  
    __constexpr_copysign(_A1 __x, _A2 __y) _NOEXCEPT {  
    ^  
In file included from /Users/jsanchez/git/devsim/src/math/CompressedMatrix.cc:8:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/CompressedMatrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/Matrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/dsMathTypes.hh:11:  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/complex:652:23: error: no matching function for call to '__constexpr_copysign'  
                __b = std::__constexpr_copysign(_Tp(0), __b);  
                      ^~~~~~~~~~~~~~~~~~~~~~~~~  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:630:54: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'float' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI float __constexpr_copysign(float __x, float __y) _NOEXCEPT {  
                                                     ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:634:55: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'double' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI double __constexpr_copysign(double __x, double __y) _NOEXCEPT {  
                                                      ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:639:1: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'long double' for 1st argument  
__constexpr_copysign(long double __x, long double __y) _NOEXCEPT {  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:647:5: note: candidate template ignored: requirement 'std::is_arithmetic<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>::value' was not satisfied [with _A1 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>, _A2 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>]  
    __constexpr_copysign(_A1 __x, _A2 __y) _NOEXCEPT {  
    ^  
In file included from /Users/jsanchez/git/devsim/src/math/CompressedMatrix.cc:8:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/CompressedMatrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/Matrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/dsMathTypes.hh:11:  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/complex:659:23: error: no matching function for call to '__constexpr_copysign'  
                __a = std::__constexpr_copysign(_Tp(0), __a);  
                      ^~~~~~~~~~~~~~~~~~~~~~~~~  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:630:54: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'float' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI float __constexpr_copysign(float __x, float __y) _NOEXCEPT {  
                                                     ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:634:55: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'double' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI double __constexpr_copysign(double __x, double __y) _NOEXCEPT {  
                                                      ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:639:1: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'long double' for 1st argument  
__constexpr_copysign(long double __x, long double __y) _NOEXCEPT {  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:647:5: note: candidate template ignored: requirement 'std::is_arithmetic<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>::value' was not satisfied [with _A1 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>, _A2 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>]  
    __constexpr_copysign(_A1 __x, _A2 __y) _NOEXCEPT {  
    ^  
In file included from /Users/jsanchez/git/devsim/src/math/CompressedMatrix.cc:8:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/CompressedMatrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/Matrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/dsMathTypes.hh:11:  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/complex:661:23: error: no matching function for call to '__constexpr_copysign'  
                __b = std::__constexpr_copysign(_Tp(0), __b);  
                      ^~~~~~~~~~~~~~~~~~~~~~~~~  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:630:54: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'float' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI float __constexpr_copysign(float __x, float __y) _NOEXCEPT {  
                                                     ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:634:55: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'double' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI double __constexpr_copysign(double __x, double __y) _NOEXCEPT {  
                                                      ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:639:1: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'long double' for 1st argument  
__constexpr_copysign(long double __x, long double __y) _NOEXCEPT {  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:647:5: note: candidate template ignored: requirement 'std::is_arithmetic<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>::value' was not satisfied [with _A1 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>, _A2 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>]  
    __constexpr_copysign(_A1 __x, _A2 __y) _NOEXCEPT {  
    ^  
In file included from /Users/jsanchez/git/devsim/src/math/CompressedMatrix.cc:8:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/CompressedMatrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/Matrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/dsMathTypes.hh:11:  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/complex:663:23: error: no matching function for call to '__constexpr_copysign'  
                __c = std::__constexpr_copysign(_Tp(0), __c);  
                      ^~~~~~~~~~~~~~~~~~~~~~~~~  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:630:54: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'float' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI float __constexpr_copysign(float __x, float __y) _NOEXCEPT {  
                                                     ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:634:55: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'double' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI double __constexpr_copysign(double __x, double __y) _NOEXCEPT {  
                                                      ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:639:1: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'long double' for 1st argument  
__constexpr_copysign(long double __x, long double __y) _NOEXCEPT {  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:647:5: note: candidate template ignored: requirement 'std::is_arithmetic<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>::value' was not satisfied [with _A1 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>, _A2 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>]  
    __constexpr_copysign(_A1 __x, _A2 __y) _NOEXCEPT {  
    ^  
In file included from /Users/jsanchez/git/devsim/src/math/CompressedMatrix.cc:8:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/CompressedMatrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/Matrix.hh:11:  
In file included from /Users/jsanchez/git/devsim/src/math/../math/dsMathTypes.hh:11:  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/complex:665:23: error: no matching function for call to '__constexpr_copysign'  
                __d = std::__constexpr_copysign(_Tp(0), __d);  
                      ^~~~~~~~~~~~~~~~~~~~~~~~~  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:630:54: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'float' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI float __constexpr_copysign(float __x, float __y) _NOEXCEPT {  
                                                     ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:634:55: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'double' for 1st argument  
_LIBCPP_CONSTEXPR inline _LIBCPP_HIDE_FROM_ABI double __constexpr_copysign(double __x, double __y) _NOEXCEPT {  
                                                      ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:639:1: note: candidate function not viable: no known conversion from 'boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>' to 'long double' for 1st argument  
__constexpr_copysign(long double __x, long double __y) _NOEXCEPT {  
^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.2.sdk/usr/include/c++/v1/cmath:647:5: note: candidate template ignored: requirement 'std::is_arithmetic<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>>::value' was not satisfied [with _A1 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>, _A2 = boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_2, void, short, -16382, 16383>, boost::multiprecision::et_off>]  
    __constexpr_copysign(_A1 __x, _A2 __y) _NOEXCEPT {  
    ^  
14 errors generated.  
make[2]: *** [src/math/CMakeFiles/math.dir/CompressedMatrix.cc.o] Error 1  
make[1]: *** [src/math/CMakeFiles/math.dir/all] Error 2  
make: *** [all] Error 2  
```

---

## Comment 1

> Username: tcaduser  
> Created at: 2024-02-24 03:37:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/600#issuecomment-1962237586  

Setting the isysroot to 13.3 seems to fix the problem:  
```  
CMAKE_OSX_SYSROOT:PATH=/Library/Developer/CommandLineTools/SDKs/MacOSX13.3.sdk  
```

---

## Comment 2

> Username: tcaduser  
> Created at: 2024-02-24 03:55:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/600#issuecomment-1962241309  

Apple clang version 15.0.0 (clang-1500.1.0.2.5)

---

## Comment 3

> Username: jzmaddock  
> Created at: 2024-02-24 09:36:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/600#issuecomment-1962311548  

I have a hunch that you are using `std::complex<cpp_bin_float<>>`, and unfortunately, std::complex is specified in a way that means it cannot be used with anything other than float, double and long double.  You may get lucky with some std lib versions where it will work "by accident",  What the std actually says is "The effect of instantiating the template complex for any type that is not a cv-unqualified floating-point type (6.8.2) is unspecified."  
  
For this reason we provide are own complex number wrappers: https://www.boost.org/doc/libs/1_84_0/libs/multiprecision/doc/html/boost_multiprecision/tut/complex/cpp_complex.html

---

## Comment 4

> Username: tcaduser  
> Created at: 2024-02-24 19:47:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/600#issuecomment-1962646510  

Thank you @jzmaddock, that is exactly the issue.
