# #394 - compile error when #define BOOST_MPL_CFG_NO_PREPROCESSED_HEADERS [Open]

> Username: idailylife  
> Created at: 2022-09-20 01:35:55 UTC  
> Updated at: 2022-09-20 01:35:55 UTC  
> Url: https://github.com/boostorg/python/issues/394  

I have tried on MSVC 14.2(Windows), gcc 9.4(Ubuntu) with Python 3.8 or 3.9.  
  
To reproduce:  
  
```  
// main.cpp  
#define BOOST_MPL_CFG_NO_PREPROCESSED_HEADERS  
  
#include <boost/python/object.hpp>  
  
int main(int argc, char* argv[]) {  
    return 0;  
}  
```  
  
```  
# CMakeLists.txt  
cmake_minimum_required(VERSION 3.0)  
  
set(CMAKE_BUILD_TYPE "Release")  
project(bp_mpl)  
set(TARGET_PLATFORM x64)  
set(CMAKE_CXX_FLAGS "-Wall -std=c++17")  
set(CMAKE_CXX_FLAGS_RELEASE "-O3 -DNDEBUG")  
set(Boost_USE_STATIC_LIBS ON)  
set(Boost_USE_MULTITHREADED ON)  
set(Boost_USE_STATIC_RUNTIME OFF)  
set(BOOST_COMPONENTS  
    date_time  
    math_c99  
    program_options  
    random  
    serialization  
    unit_test_framework  
    python3  
)  
  
find_package(Boost REQUIRED COMPONENTS ${BOOST_COMPONENTS})  
set(PY_VERSION "3")  
find_package(PythonInterp ${PY_VERSION} EXACT REQUIRED)  
find_package(PythonLibs ${PY_VERSION} EXACT REQUIRED)  
  
add_executable(  
    main_exec  
    main.cpp  
)  
  
target_include_directories(main_exec PUBLIC ${Boost_INCLUDE_DIRS} ${PYTHON_INCLUDE_DIRS})  
target_link_libraries(main_exec PUBLIC ${Boost_LIBRARIES} ${PYTHON_LIBRARIES})  
```  
  
  
```  
~/code/boost_py_mpl/build$ make main_exec  
-- Configuring done  
-- Generating done  
-- Build files have been written to: /home/NA/code/boost_py_mpl/build  
[ 50%] Building CXX object CMakeFiles/main_exec.dir/main.cpp.o  
In file included from /usr/local/include/boost/mpl/aux_/integral_wrapper.hpp:22,  
                 from /usr/local/include/boost/mpl/int.hpp:20,  
                 from /usr/local/include/boost/mpl/lambda_fwd.hpp:23,  
                 from /usr/local/include/boost/mpl/aux_/na_spec.hpp:18,  
                 from /usr/local/include/boost/mpl/if.hpp:19,  
                 from /usr/local/include/boost/python/object/forward.hpp:8,  
                 from /usr/local/include/boost/python/object/pointer_holder.hpp:16,  
                 from /usr/local/include/boost/python/to_python_indirect.hpp:10,  
                 from /usr/local/include/boost/python/converter/arg_to_python.hpp:10,  
                 from /usr/local/include/boost/python/call.hpp:15,  
                 from /usr/local/include/boost/python/object_core.hpp:14,  
                 from /usr/local/include/boost/python/object.hpp:9,  
                 from /home/NA/code/boost_py_mpl/main.cpp:3:  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:177:48: error: ‘BOOST_PP_ITERATION_02’ was not declared in this scope; did you mean ‘BOOST_PP_ITERATION_05’?  
  177 |     : AUX778076_OP_N_CALLS(AUX778076_OP_ARITY, N)  
      |                                                ^  
In file included from /usr/local/include/boost/mpl/aux_/arithmetic_op.hpp:26,  
                 from /usr/local/include/boost/mpl/plus.hpp:19,  
                 from /usr/local/include/boost/mpl/vector/aux_/iterator.hpp:19,  
                 from /usr/local/include/boost/mpl/vector/aux_/vector0.hpp:22,  
                 from /usr/local/include/boost/mpl/vector/aux_/clear.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector0.hpp:24,  
                 from /usr/local/include/boost/mpl/vector/vector10.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector20.hpp:18,  
                 from /usr/local/include/boost/python/detail/type_list.hpp:28,  
                 from /usr/local/include/boost/python/signature.hpp:26,  
                 from /usr/local/include/boost/python/object/function_handle.hpp:11,  
                 from /usr/local/include/boost/python/converter/arg_to_python.hpp:19,  
                 from /usr/local/include/boost/python/call.hpp:15,  
                 from /usr/local/include/boost/python/object_core.hpp:14,  
                 from /usr/local/include/boost/python/object.hpp:9,  
                 from /home/NA/code/boost_py_mpl/main.cpp:3:  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:177:7: error: template argument 2 is invalid  
  177 |     : AUX778076_OP_N_CALLS(AUX778076_OP_ARITY, N)  
      |       ^~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:177:7: error: template argument 1 is invalid  
  177 |     : AUX778076_OP_N_CALLS(AUX778076_OP_ARITY, N)  
      |       ^~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:177:7: error: template argument 1 is invalid  
  177 |     : AUX778076_OP_N_CALLS(AUX778076_OP_ARITY, N)  
      |       ^~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:177:7: error: template argument 1 is invalid  
  177 |     : AUX778076_OP_N_CALLS(AUX778076_OP_ARITY, N)  
      |       ^~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/mpl/aux_/integral_wrapper.hpp:22,  
                 from /usr/local/include/boost/mpl/int.hpp:20,  
                 from /usr/local/include/boost/mpl/lambda_fwd.hpp:23,  
                 from /usr/local/include/boost/mpl/aux_/na_spec.hpp:18,  
                 from /usr/local/include/boost/mpl/if.hpp:19,  
                 from /usr/local/include/boost/python/object/forward.hpp:8,  
                 from /usr/local/include/boost/python/object/pointer_holder.hpp:16,  
                 from /usr/local/include/boost/python/to_python_indirect.hpp:10,  
                 from /usr/local/include/boost/python/converter/arg_to_python.hpp:10,  
                 from /usr/local/include/boost/python/call.hpp:15,  
                 from /usr/local/include/boost/python/object_core.hpp:14,  
                 from /usr/local/include/boost/python/object.hpp:9,  
                 from /home/NA/code/boost_py_mpl/main.cpp:3:  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:298:40: error: expected nested-name-specifier before numeric constant  
  298 |       BOOST_MPL_PP_PARAMS(i_, typename N)  
      |                                        ^  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:298:40: error: expected ‘>’ before numeric constant  
In file included from /usr/local/include/boost/preprocessor/iteration/detail/iter/limits/reverse1_256.hpp:1274,  
                 from /usr/local/include/boost/preprocessor/iteration/detail/iter/reverse1.hpp:1309,  
                 from /usr/local/include/boost/preprocessor/iteration/detail/iter/forward1.hpp:45,  
                 from /usr/local/include/boost/mpl/aux_/numeric_op.hpp:188,  
                 from /usr/local/include/boost/mpl/aux_/arithmetic_op.hpp:26,  
                 from /usr/local/include/boost/mpl/plus.hpp:19,  
                 from /usr/local/include/boost/mpl/vector/aux_/iterator.hpp:19,  
                 from /usr/local/include/boost/mpl/vector/aux_/vector0.hpp:22,  
                 from /usr/local/include/boost/mpl/vector/aux_/clear.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector0.hpp:24,  
                 from /usr/local/include/boost/mpl/vector/vector10.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector20.hpp:18,  
                 from /usr/local/include/boost/python/detail/type_list.hpp:28,  
                 from /usr/local/include/boost/python/signature.hpp:26,  
                 from /usr/local/include/boost/python/object/function_handle.hpp:11,  
                 from /usr/local/include/boost/python/converter/arg_to_python.hpp:19,  
                 from /usr/local/include/boost/python/call.hpp:15,  
                 from /usr/local/include/boost/python/object_core.hpp:14,  
                 from /usr/local/include/boost/python/object.hpp:9,  
                 from /home/NA/code/boost_py_mpl/main.cpp:3:  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: error: type/value mismatch at argument 1 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::plus’  
  300 | struct AUX778076_OP_NAME<BOOST_MPL_PP_PARTIAL_SPEC_PARAMS(i_, N, na)>  
      |                                                                     ^  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: note:   expected a type, got ‘41’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: error: type/value mismatch at argument 2 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::plus’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: note:   expected a type, got ‘42’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: error: type/value mismatch at argument 3 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::plus’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: note:   expected a type, got ‘43’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: error: type/value mismatch at argument 4 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::plus’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: note:   expected a type, got ‘44’  
In file included from /usr/local/include/boost/mpl/aux_/arithmetic_op.hpp:26,  
                 from /usr/local/include/boost/mpl/plus.hpp:19,  
                 from /usr/local/include/boost/mpl/vector/aux_/iterator.hpp:19,  
                 from /usr/local/include/boost/mpl/vector/aux_/vector0.hpp:22,  
                 from /usr/local/include/boost/mpl/vector/aux_/clear.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector0.hpp:24,  
                 from /usr/local/include/boost/mpl/vector/vector10.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector20.hpp:18,  
                 from /usr/local/include/boost/python/detail/type_list.hpp:28,  
                 from /usr/local/include/boost/python/signature.hpp:26,  
                 from /usr/local/include/boost/python/object/function_handle.hpp:11,  
                 from /usr/local/include/boost/python/converter/arg_to_python.hpp:19,  
                 from /usr/local/include/boost/python/call.hpp:15,  
                 from /usr/local/include/boost/python/object_core.hpp:14,  
                 from /usr/local/include/boost/python/object.hpp:9,  
                 from /home/NA/code/boost_py_mpl/main.cpp:3:  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: ‘N1’ was not declared in this scope; did you mean ‘y1’?  
  302 |     : AUX778076_OP_N_CALLS(i_, N)  
      |       ^~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: template argument 1 is invalid  
  302 |     : AUX778076_OP_N_CALLS(i_, N)  
      |       ^~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: type/value mismatch at argument 2 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::plus’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: note:   expected a type, got ‘42’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: template argument 1 is invalid  
  302 |     : AUX778076_OP_N_CALLS(i_, N)  
      |       ^~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: type/value mismatch at argument 2 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::plus’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: note:   expected a type, got ‘43’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: template argument 1 is invalid  
  302 |     : AUX778076_OP_N_CALLS(i_, N)  
      |       ^~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: type/value mismatch at argument 2 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::plus’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: note:   expected a type, got ‘44’  
In file included from /usr/local/include/boost/mpl/aux_/integral_wrapper.hpp:22,  
                 from /usr/local/include/boost/mpl/int.hpp:20,  
                 from /usr/local/include/boost/mpl/lambda_fwd.hpp:23,  
                 from /usr/local/include/boost/mpl/aux_/na_spec.hpp:18,  
                 from /usr/local/include/boost/mpl/if.hpp:19,  
                 from /usr/local/include/boost/python/object/forward.hpp:8,  
                 from /usr/local/include/boost/python/object/pointer_holder.hpp:16,  
                 from /usr/local/include/boost/python/to_python_indirect.hpp:10,  
                 from /usr/local/include/boost/python/converter/arg_to_python.hpp:10,  
                 from /usr/local/include/boost/python/call.hpp:15,  
                 from /usr/local/include/boost/python/object_core.hpp:14,  
                 from /usr/local/include/boost/python/object.hpp:9,  
                 from /home/NA/code/boost_py_mpl/main.cpp:3:  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:298:40: error: expected nested-name-specifier before numeric constant  
  298 |       BOOST_MPL_PP_PARAMS(i_, typename N)  
      |                                        ^  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:298:40: error: expected ‘>’ before numeric constant  
In file included from /usr/local/include/boost/preprocessor/iteration/detail/iter/limits/reverse1_256.hpp:1279,  
                 from /usr/local/include/boost/preprocessor/iteration/detail/iter/reverse1.hpp:1309,  
                 from /usr/local/include/boost/preprocessor/iteration/detail/iter/forward1.hpp:45,  
                 from /usr/local/include/boost/mpl/aux_/numeric_op.hpp:188,  
                 from /usr/local/include/boost/mpl/aux_/arithmetic_op.hpp:26,  
                 from /usr/local/include/boost/mpl/plus.hpp:19,  
                 from /usr/local/include/boost/mpl/vector/aux_/iterator.hpp:19,  
                 from /usr/local/include/boost/mpl/vector/aux_/vector0.hpp:22,  
                 from /usr/local/include/boost/mpl/vector/aux_/clear.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector0.hpp:24,  
                 from /usr/local/include/boost/mpl/vector/vector10.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector20.hpp:18,  
                 from /usr/local/include/boost/python/detail/type_list.hpp:28,  
                 from /usr/local/include/boost/python/signature.hpp:26,  
                 from /usr/local/include/boost/python/object/function_handle.hpp:11,  
                 from /usr/local/include/boost/python/converter/arg_to_python.hpp:19,  
                 from /usr/local/include/boost/python/call.hpp:15,  
                 from /usr/local/include/boost/python/object_core.hpp:14,  
                 from /usr/local/include/boost/python/object.hpp:9,  
                 from /home/NA/code/boost_py_mpl/main.cpp:3:  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: error: type/value mismatch at argument 1 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::plus’  
  300 | struct AUX778076_OP_NAME<BOOST_MPL_PP_PARTIAL_SPEC_PARAMS(i_, N, na)>  
      |                                                                     ^  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: note:   expected a type, got ‘31’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: error: type/value mismatch at argument 2 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::plus’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: note:   expected a type, got ‘32’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: error: type/value mismatch at argument 3 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::plus’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: note:   expected a type, got ‘33’  
In file included from /usr/local/include/boost/mpl/aux_/arithmetic_op.hpp:26,  
                 from /usr/local/include/boost/mpl/plus.hpp:19,  
                 from /usr/local/include/boost/mpl/vector/aux_/iterator.hpp:19,  
                 from /usr/local/include/boost/mpl/vector/aux_/vector0.hpp:22,  
                 from /usr/local/include/boost/mpl/vector/aux_/clear.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector0.hpp:24,  
                 from /usr/local/include/boost/mpl/vector/vector10.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector20.hpp:18,  
                 from /usr/local/include/boost/python/detail/type_list.hpp:28,  
                 from /usr/local/include/boost/python/signature.hpp:26,  
                 from /usr/local/include/boost/python/object/function_handle.hpp:11,  
                 from /usr/local/include/boost/python/converter/arg_to_python.hpp:19,  
                 from /usr/local/include/boost/python/call.hpp:15,  
                 from /usr/local/include/boost/python/object_core.hpp:14,  
                 from /usr/local/include/boost/python/object.hpp:9,  
                 from /home/NA/code/boost_py_mpl/main.cpp:3:  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: ‘N1’ was not declared in this scope; did you mean ‘y1’?  
  302 |     : AUX778076_OP_N_CALLS(i_, N)  
      |       ^~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: template argument 1 is invalid  
  302 |     : AUX778076_OP_N_CALLS(i_, N)  
      |       ^~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: type/value mismatch at argument 2 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::plus’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: note:   expected a type, got ‘32’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: template argument 1 is invalid  
  302 |     : AUX778076_OP_N_CALLS(i_, N)  
      |       ^~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: type/value mismatch at argument 2 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::plus’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: note:   expected a type, got ‘33’  
In file included from /usr/local/include/boost/mpl/aux_/integral_wrapper.hpp:22,  
                 from /usr/local/include/boost/mpl/int.hpp:20,  
                 from /usr/local/include/boost/mpl/lambda_fwd.hpp:23,  
                 from /usr/local/include/boost/mpl/aux_/na_spec.hpp:18,  
                 from /usr/local/include/boost/mpl/if.hpp:19,  
                 from /usr/local/include/boost/python/object/forward.hpp:8,  
                 from /usr/local/include/boost/python/object/pointer_holder.hpp:16,  
                 from /usr/local/include/boost/python/to_python_indirect.hpp:10,  
                 from /usr/local/include/boost/python/converter/arg_to_python.hpp:10,  
                 from /usr/local/include/boost/python/call.hpp:15,  
                 from /usr/local/include/boost/python/object_core.hpp:14,  
                 from /usr/local/include/boost/python/object.hpp:9,  
                 from /home/NA/code/boost_py_mpl/main.cpp:3:  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:298:40: error: expected nested-name-specifier before numeric constant  
  298 |       BOOST_MPL_PP_PARAMS(i_, typename N)  
      |                                        ^  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:298:40: error: expected ‘>’ before numeric constant  
In file included from /usr/local/include/boost/preprocessor/iteration/detail/iter/limits/reverse1_256.hpp:1284,  
                 from /usr/local/include/boost/preprocessor/iteration/detail/iter/reverse1.hpp:1309,  
                 from /usr/local/include/boost/preprocessor/iteration/detail/iter/forward1.hpp:45,  
                 from /usr/local/include/boost/mpl/aux_/numeric_op.hpp:188,  
                 from /usr/local/include/boost/mpl/aux_/arithmetic_op.hpp:26,  
                 from /usr/local/include/boost/mpl/plus.hpp:19,  
                 from /usr/local/include/boost/mpl/vector/aux_/iterator.hpp:19,  
                 from /usr/local/include/boost/mpl/vector/aux_/vector0.hpp:22,  
                 from /usr/local/include/boost/mpl/vector/aux_/clear.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector0.hpp:24,  
                 from /usr/local/include/boost/mpl/vector/vector10.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector20.hpp:18,  
                 from /usr/local/include/boost/python/detail/type_list.hpp:28,  
                 from /usr/local/include/boost/python/signature.hpp:26,  
                 from /usr/local/include/boost/python/object/function_handle.hpp:11,  
                 from /usr/local/include/boost/python/converter/arg_to_python.hpp:19,  
                 from /usr/local/include/boost/python/call.hpp:15,  
                 from /usr/local/include/boost/python/object_core.hpp:14,  
                 from /usr/local/include/boost/python/object.hpp:9,  
                 from /home/NA/code/boost_py_mpl/main.cpp:3:  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: error: type/value mismatch at argument 1 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::plus’  
  300 | struct AUX778076_OP_NAME<BOOST_MPL_PP_PARTIAL_SPEC_PARAMS(i_, N, na)>  
      |                                                                     ^  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: note:   expected a type, got ‘21’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: error: type/value mismatch at argument 2 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::plus’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: note:   expected a type, got ‘22’  
In file included from /usr/local/include/boost/mpl/aux_/arithmetic_op.hpp:26,  
                 from /usr/local/include/boost/mpl/plus.hpp:19,  
                 from /usr/local/include/boost/mpl/vector/aux_/iterator.hpp:19,  
                 from /usr/local/include/boost/mpl/vector/aux_/vector0.hpp:22,  
                 from /usr/local/include/boost/mpl/vector/aux_/clear.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector0.hpp:24,  
                 from /usr/local/include/boost/mpl/vector/vector10.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector20.hpp:18,  
                 from /usr/local/include/boost/python/detail/type_list.hpp:28,  
                 from /usr/local/include/boost/python/signature.hpp:26,  
                 from /usr/local/include/boost/python/object/function_handle.hpp:11,  
                 from /usr/local/include/boost/python/converter/arg_to_python.hpp:19,  
                 from /usr/local/include/boost/python/call.hpp:15,  
                 from /usr/local/include/boost/python/object_core.hpp:14,  
                 from /usr/local/include/boost/python/object.hpp:9,  
                 from /home/NA/code/boost_py_mpl/main.cpp:3:  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:251:42: error: ‘N1’ was not declared in this scope; did you mean ‘y1’?  
  251 |           typename AUX778076_OP_TAG_NAME<N1>::type  
      |                                          ^~  
      |                                          y1  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:251:44: error: template argument 1 is invalid  
  251 |           typename AUX778076_OP_TAG_NAME<N1>::type  
      |                                            ^  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:252:42: error: ‘N2’ was not declared in this scope  
  252 |         , typename AUX778076_OP_TAG_NAME<N2>::type  
      |                                          ^~  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:252:44: error: template argument 1 is invalid  
  252 |         , typename AUX778076_OP_TAG_NAME<N2>::type  
      |                                            ^  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:253:9: error: template argument 1 is invalid  
  253 |         >::template apply<N1,N2>::type  
      |         ^  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:253:9: error: template argument 2 is invalid  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:253:12: error: expected class-name before ‘template’  
  253 |         >::template apply<N1,N2>::type  
      |            ^~~~~~~~  
In file included from /usr/local/include/boost/mpl/aux_/integral_wrapper.hpp:22,  
                 from /usr/local/include/boost/mpl/int.hpp:20,  
                 from /usr/local/include/boost/mpl/lambda_fwd.hpp:23,  
                 from /usr/local/include/boost/mpl/aux_/na_spec.hpp:18,  
                 from /usr/local/include/boost/mpl/if.hpp:19,  
                 from /usr/local/include/boost/python/object/forward.hpp:8,  
                 from /usr/local/include/boost/python/object/pointer_holder.hpp:16,  
                 from /usr/local/include/boost/python/to_python_indirect.hpp:10,  
                 from /usr/local/include/boost/python/converter/arg_to_python.hpp:10,  
                 from /usr/local/include/boost/python/call.hpp:15,  
                 from /usr/local/include/boost/python/object_core.hpp:14,  
                 from /usr/local/include/boost/python/object.hpp:9,  
                 from /home/NA/code/boost_py_mpl/main.cpp:3:  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:177:48: error: ‘BOOST_PP_ITERATION_02’ was not declared in this scope; did you mean ‘BOOST_PP_ITERATION_05’?  
  177 |     : AUX778076_OP_N_CALLS(AUX778076_OP_ARITY, N)  
      |                                                ^  
In file included from /usr/local/include/boost/mpl/aux_/arithmetic_op.hpp:26,  
                 from /usr/local/include/boost/mpl/minus.hpp:19,  
                 from /usr/local/include/boost/mpl/vector/aux_/iterator.hpp:20,  
                 from /usr/local/include/boost/mpl/vector/aux_/vector0.hpp:22,  
                 from /usr/local/include/boost/mpl/vector/aux_/clear.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector0.hpp:24,  
                 from /usr/local/include/boost/mpl/vector/vector10.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector20.hpp:18,  
                 from /usr/local/include/boost/python/detail/type_list.hpp:28,  
                 from /usr/local/include/boost/python/signature.hpp:26,  
                 from /usr/local/include/boost/python/object/function_handle.hpp:11,  
                 from /usr/local/include/boost/python/converter/arg_to_python.hpp:19,  
                 from /usr/local/include/boost/python/call.hpp:15,  
                 from /usr/local/include/boost/python/object_core.hpp:14,  
                 from /usr/local/include/boost/python/object.hpp:9,  
                 from /home/NA/code/boost_py_mpl/main.cpp:3:  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:177:7: error: template argument 2 is invalid  
  177 |     : AUX778076_OP_N_CALLS(AUX778076_OP_ARITY, N)  
      |       ^~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:177:7: error: template argument 1 is invalid  
  177 |     : AUX778076_OP_N_CALLS(AUX778076_OP_ARITY, N)  
      |       ^~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:177:7: error: template argument 1 is invalid  
  177 |     : AUX778076_OP_N_CALLS(AUX778076_OP_ARITY, N)  
      |       ^~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:177:7: error: template argument 1 is invalid  
  177 |     : AUX778076_OP_N_CALLS(AUX778076_OP_ARITY, N)  
      |       ^~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/mpl/aux_/integral_wrapper.hpp:22,  
                 from /usr/local/include/boost/mpl/int.hpp:20,  
                 from /usr/local/include/boost/mpl/lambda_fwd.hpp:23,  
                 from /usr/local/include/boost/mpl/aux_/na_spec.hpp:18,  
                 from /usr/local/include/boost/mpl/if.hpp:19,  
                 from /usr/local/include/boost/python/object/forward.hpp:8,  
                 from /usr/local/include/boost/python/object/pointer_holder.hpp:16,  
                 from /usr/local/include/boost/python/to_python_indirect.hpp:10,  
                 from /usr/local/include/boost/python/converter/arg_to_python.hpp:10,  
                 from /usr/local/include/boost/python/call.hpp:15,  
                 from /usr/local/include/boost/python/object_core.hpp:14,  
                 from /usr/local/include/boost/python/object.hpp:9,  
                 from /home/NA/code/boost_py_mpl/main.cpp:3:  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:298:40: error: expected nested-name-specifier before numeric constant  
  298 |       BOOST_MPL_PP_PARAMS(i_, typename N)  
      |                                        ^  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:298:40: error: expected ‘>’ before numeric constant  
In file included from /usr/local/include/boost/preprocessor/iteration/detail/iter/limits/reverse1_256.hpp:1274,  
                 from /usr/local/include/boost/preprocessor/iteration/detail/iter/reverse1.hpp:1309,  
                 from /usr/local/include/boost/preprocessor/iteration/detail/iter/forward1.hpp:45,  
                 from /usr/local/include/boost/mpl/aux_/numeric_op.hpp:188,  
                 from /usr/local/include/boost/mpl/aux_/arithmetic_op.hpp:26,  
                 from /usr/local/include/boost/mpl/minus.hpp:19,  
                 from /usr/local/include/boost/mpl/vector/aux_/iterator.hpp:20,  
                 from /usr/local/include/boost/mpl/vector/aux_/vector0.hpp:22,  
                 from /usr/local/include/boost/mpl/vector/aux_/clear.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector0.hpp:24,  
                 from /usr/local/include/boost/mpl/vector/vector10.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector20.hpp:18,  
                 from /usr/local/include/boost/python/detail/type_list.hpp:28,  
                 from /usr/local/include/boost/python/signature.hpp:26,  
                 from /usr/local/include/boost/python/object/function_handle.hpp:11,  
                 from /usr/local/include/boost/python/converter/arg_to_python.hpp:19,  
                 from /usr/local/include/boost/python/call.hpp:15,  
                 from /usr/local/include/boost/python/object_core.hpp:14,  
                 from /usr/local/include/boost/python/object.hpp:9,  
                 from /home/NA/code/boost_py_mpl/main.cpp:3:  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: error: type/value mismatch at argument 1 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::minus’  
  300 | struct AUX778076_OP_NAME<BOOST_MPL_PP_PARTIAL_SPEC_PARAMS(i_, N, na)>  
      |                                                                     ^  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: note:   expected a type, got ‘41’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: error: type/value mismatch at argument 2 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::minus’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: note:   expected a type, got ‘42’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: error: type/value mismatch at argument 3 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::minus’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: note:   expected a type, got ‘43’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: error: type/value mismatch at argument 4 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::minus’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: note:   expected a type, got ‘44’  
In file included from /usr/local/include/boost/mpl/aux_/arithmetic_op.hpp:26,  
                 from /usr/local/include/boost/mpl/minus.hpp:19,  
                 from /usr/local/include/boost/mpl/vector/aux_/iterator.hpp:20,  
                 from /usr/local/include/boost/mpl/vector/aux_/vector0.hpp:22,  
                 from /usr/local/include/boost/mpl/vector/aux_/clear.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector0.hpp:24,  
                 from /usr/local/include/boost/mpl/vector/vector10.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector20.hpp:18,  
                 from /usr/local/include/boost/python/detail/type_list.hpp:28,  
                 from /usr/local/include/boost/python/signature.hpp:26,  
                 from /usr/local/include/boost/python/object/function_handle.hpp:11,  
                 from /usr/local/include/boost/python/converter/arg_to_python.hpp:19,  
                 from /usr/local/include/boost/python/call.hpp:15,  
                 from /usr/local/include/boost/python/object_core.hpp:14,  
                 from /usr/local/include/boost/python/object.hpp:9,  
                 from /home/NA/code/boost_py_mpl/main.cpp:3:  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: ‘N1’ was not declared in this scope; did you mean ‘y1’?  
  302 |     : AUX778076_OP_N_CALLS(i_, N)  
      |       ^~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: template argument 1 is invalid  
  302 |     : AUX778076_OP_N_CALLS(i_, N)  
      |       ^~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: type/value mismatch at argument 2 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::minus’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: note:   expected a type, got ‘42’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: template argument 1 is invalid  
  302 |     : AUX778076_OP_N_CALLS(i_, N)  
      |       ^~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: type/value mismatch at argument 2 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::minus’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: note:   expected a type, got ‘43’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: template argument 1 is invalid  
  302 |     : AUX778076_OP_N_CALLS(i_, N)  
      |       ^~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: type/value mismatch at argument 2 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::minus’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: note:   expected a type, got ‘44’  
In file included from /usr/local/include/boost/mpl/aux_/integral_wrapper.hpp:22,  
                 from /usr/local/include/boost/mpl/int.hpp:20,  
                 from /usr/local/include/boost/mpl/lambda_fwd.hpp:23,  
                 from /usr/local/include/boost/mpl/aux_/na_spec.hpp:18,  
                 from /usr/local/include/boost/mpl/if.hpp:19,  
                 from /usr/local/include/boost/python/object/forward.hpp:8,  
                 from /usr/local/include/boost/python/object/pointer_holder.hpp:16,  
                 from /usr/local/include/boost/python/to_python_indirect.hpp:10,  
                 from /usr/local/include/boost/python/converter/arg_to_python.hpp:10,  
                 from /usr/local/include/boost/python/call.hpp:15,  
                 from /usr/local/include/boost/python/object_core.hpp:14,  
                 from /usr/local/include/boost/python/object.hpp:9,  
                 from /home/NA/code/boost_py_mpl/main.cpp:3:  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:298:40: error: expected nested-name-specifier before numeric constant  
  298 |       BOOST_MPL_PP_PARAMS(i_, typename N)  
      |                                        ^  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:298:40: error: expected ‘>’ before numeric constant  
In file included from /usr/local/include/boost/preprocessor/iteration/detail/iter/limits/reverse1_256.hpp:1279,  
                 from /usr/local/include/boost/preprocessor/iteration/detail/iter/reverse1.hpp:1309,  
                 from /usr/local/include/boost/preprocessor/iteration/detail/iter/forward1.hpp:45,  
                 from /usr/local/include/boost/mpl/aux_/numeric_op.hpp:188,  
                 from /usr/local/include/boost/mpl/aux_/arithmetic_op.hpp:26,  
                 from /usr/local/include/boost/mpl/minus.hpp:19,  
                 from /usr/local/include/boost/mpl/vector/aux_/iterator.hpp:20,  
                 from /usr/local/include/boost/mpl/vector/aux_/vector0.hpp:22,  
                 from /usr/local/include/boost/mpl/vector/aux_/clear.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector0.hpp:24,  
                 from /usr/local/include/boost/mpl/vector/vector10.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector20.hpp:18,  
                 from /usr/local/include/boost/python/detail/type_list.hpp:28,  
                 from /usr/local/include/boost/python/signature.hpp:26,  
                 from /usr/local/include/boost/python/object/function_handle.hpp:11,  
                 from /usr/local/include/boost/python/converter/arg_to_python.hpp:19,  
                 from /usr/local/include/boost/python/call.hpp:15,  
                 from /usr/local/include/boost/python/object_core.hpp:14,  
                 from /usr/local/include/boost/python/object.hpp:9,  
                 from /home/NA/code/boost_py_mpl/main.cpp:3:  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: error: type/value mismatch at argument 1 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::minus’  
  300 | struct AUX778076_OP_NAME<BOOST_MPL_PP_PARTIAL_SPEC_PARAMS(i_, N, na)>  
      |                                                                     ^  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: note:   expected a type, got ‘31’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: error: type/value mismatch at argument 2 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::minus’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: note:   expected a type, got ‘32’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: error: type/value mismatch at argument 3 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::minus’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: note:   expected a type, got ‘33’  
In file included from /usr/local/include/boost/mpl/aux_/arithmetic_op.hpp:26,  
                 from /usr/local/include/boost/mpl/minus.hpp:19,  
                 from /usr/local/include/boost/mpl/vector/aux_/iterator.hpp:20,  
                 from /usr/local/include/boost/mpl/vector/aux_/vector0.hpp:22,  
                 from /usr/local/include/boost/mpl/vector/aux_/clear.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector0.hpp:24,  
                 from /usr/local/include/boost/mpl/vector/vector10.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector20.hpp:18,  
                 from /usr/local/include/boost/python/detail/type_list.hpp:28,  
                 from /usr/local/include/boost/python/signature.hpp:26,  
                 from /usr/local/include/boost/python/object/function_handle.hpp:11,  
                 from /usr/local/include/boost/python/converter/arg_to_python.hpp:19,  
                 from /usr/local/include/boost/python/call.hpp:15,  
                 from /usr/local/include/boost/python/object_core.hpp:14,  
                 from /usr/local/include/boost/python/object.hpp:9,  
                 from /home/NA/code/boost_py_mpl/main.cpp:3:  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: ‘N1’ was not declared in this scope; did you mean ‘y1’?  
  302 |     : AUX778076_OP_N_CALLS(i_, N)  
      |       ^~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: template argument 1 is invalid  
  302 |     : AUX778076_OP_N_CALLS(i_, N)  
      |       ^~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: type/value mismatch at argument 2 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::minus’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: note:   expected a type, got ‘32’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: template argument 1 is invalid  
  302 |     : AUX778076_OP_N_CALLS(i_, N)  
      |       ^~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: error: type/value mismatch at argument 2 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::minus’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:302:7: note:   expected a type, got ‘33’  
In file included from /usr/local/include/boost/mpl/aux_/integral_wrapper.hpp:22,  
                 from /usr/local/include/boost/mpl/int.hpp:20,  
                 from /usr/local/include/boost/mpl/lambda_fwd.hpp:23,  
                 from /usr/local/include/boost/mpl/aux_/na_spec.hpp:18,  
                 from /usr/local/include/boost/mpl/if.hpp:19,  
                 from /usr/local/include/boost/python/object/forward.hpp:8,  
                 from /usr/local/include/boost/python/object/pointer_holder.hpp:16,  
                 from /usr/local/include/boost/python/to_python_indirect.hpp:10,  
                 from /usr/local/include/boost/python/converter/arg_to_python.hpp:10,  
                 from /usr/local/include/boost/python/call.hpp:15,  
                 from /usr/local/include/boost/python/object_core.hpp:14,  
                 from /usr/local/include/boost/python/object.hpp:9,  
                 from /home/NA/code/boost_py_mpl/main.cpp:3:  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:298:40: error: expected nested-name-specifier before numeric constant  
  298 |       BOOST_MPL_PP_PARAMS(i_, typename N)  
      |                                        ^  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:298:40: error: expected ‘>’ before numeric constant  
In file included from /usr/local/include/boost/preprocessor/iteration/detail/iter/limits/reverse1_256.hpp:1284,  
                 from /usr/local/include/boost/preprocessor/iteration/detail/iter/reverse1.hpp:1309,  
                 from /usr/local/include/boost/preprocessor/iteration/detail/iter/forward1.hpp:45,  
                 from /usr/local/include/boost/mpl/aux_/numeric_op.hpp:188,  
                 from /usr/local/include/boost/mpl/aux_/arithmetic_op.hpp:26,  
                 from /usr/local/include/boost/mpl/minus.hpp:19,  
                 from /usr/local/include/boost/mpl/vector/aux_/iterator.hpp:20,  
                 from /usr/local/include/boost/mpl/vector/aux_/vector0.hpp:22,  
                 from /usr/local/include/boost/mpl/vector/aux_/clear.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector0.hpp:24,  
                 from /usr/local/include/boost/mpl/vector/vector10.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector20.hpp:18,  
                 from /usr/local/include/boost/python/detail/type_list.hpp:28,  
                 from /usr/local/include/boost/python/signature.hpp:26,  
                 from /usr/local/include/boost/python/object/function_handle.hpp:11,  
                 from /usr/local/include/boost/python/converter/arg_to_python.hpp:19,  
                 from /usr/local/include/boost/python/call.hpp:15,  
                 from /usr/local/include/boost/python/object_core.hpp:14,  
                 from /usr/local/include/boost/python/object.hpp:9,  
                 from /home/NA/code/boost_py_mpl/main.cpp:3:  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: error: type/value mismatch at argument 1 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::minus’  
  300 | struct AUX778076_OP_NAME<BOOST_MPL_PP_PARTIAL_SPEC_PARAMS(i_, N, na)>  
      |                                                                     ^  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: note:   expected a type, got ‘21’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: error: type/value mismatch at argument 2 in template parameter list for ‘template<class N1, class N2, class BOOST_PP_ITERATION_03, class BOOST_PP_ITERATION_04, class BOOST_PP_ITERATION_05> struct boost::mpl::minus’  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:300:69: note:   expected a type, got ‘22’  
In file included from /usr/local/include/boost/mpl/aux_/arithmetic_op.hpp:26,  
                 from /usr/local/include/boost/mpl/minus.hpp:19,  
                 from /usr/local/include/boost/mpl/vector/aux_/iterator.hpp:20,  
                 from /usr/local/include/boost/mpl/vector/aux_/vector0.hpp:22,  
                 from /usr/local/include/boost/mpl/vector/aux_/clear.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector0.hpp:24,  
                 from /usr/local/include/boost/mpl/vector/vector10.hpp:18,  
                 from /usr/local/include/boost/mpl/vector/vector20.hpp:18,  
                 from /usr/local/include/boost/python/detail/type_list.hpp:28,  
                 from /usr/local/include/boost/python/signature.hpp:26,  
                 from /usr/local/include/boost/python/object/function_handle.hpp:11,  
                 from /usr/local/include/boost/python/converter/arg_to_python.hpp:19,  
                 from /usr/local/include/boost/python/call.hpp:15,  
                 from /usr/local/include/boost/python/object_core.hpp:14,  
                 from /usr/local/include/boost/python/object.hpp:9,  
                 from /home/NA/code/boost_py_mpl/main.cpp:3:  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:251:42: error: ‘N1’ was not declared in this scope; did you mean ‘y1’?  
  251 |           typename AUX778076_OP_TAG_NAME<N1>::type  
      |                                          ^~  
      |                                          y1  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:251:44: error: template argument 1 is invalid  
  251 |           typename AUX778076_OP_TAG_NAME<N1>::type  
      |                                            ^  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:252:42: error: ‘N2’ was not declared in this scope  
  252 |         , typename AUX778076_OP_TAG_NAME<N2>::type  
      |                                          ^~  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:252:44: error: template argument 1 is invalid  
  252 |         , typename AUX778076_OP_TAG_NAME<N2>::type  
      |                                            ^  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:253:9: error: template argument 1 is invalid  
  253 |         >::template apply<N1,N2>::type  
      |         ^  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:253:9: error: template argument 2 is invalid  
/usr/local/include/boost/mpl/aux_/numeric_op.hpp:253:12: error: expected class-name before ‘template’  
  253 |         >::template apply<N1,N2>::type  
      |            ^~~~~~~~  
make[3]: *** [CMakeFiles/main_exec.dir/build.make:63: CMakeFiles/main_exec.dir/main.cpp.o] Error 1  
make[2]: *** [CMakeFiles/Makefile2:76: CMakeFiles/main_exec.dir/all] Error 2  
make[1]: *** [CMakeFiles/Makefile2:83: CMakeFiles/main_exec.dir/rule] Error 2  
make: *** [Makefile:118: main_exec] Error 2  
```
