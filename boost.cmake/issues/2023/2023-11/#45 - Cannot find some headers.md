# #45 - Cannot find some headers [Open]

> Username: lproj  
> Created at: 2023-11-17 00:55:30 UTC  
> Updated at: 2025-12-14 08:22:00 UTC  
> Url: https://github.com/boostorg/cmake/issues/45  

Hi,  
  
I am trying to install boost v1.83.0 with CPM.cmake, but some headers like the ones in <boost/accumulators/[...]> cannot be found in the standard path during build. Can you tell me why and/or help me please?  
  
Below are a simple CMakeLists.txt and a simple program reproducing the problem (also with boost 1.82.0...):  
  
```  
# CMakeLists.txt  
add_executable(CPMExampleBoost main.cpp)  
target_compile_features(CPMExampleBoost PRIVATE cxx_std_17)  
  
file(  
  DOWNLOAD  
  https://github.com/cpm-cmake/CPM.cmake/releases/download/v0.38.3/CPM.cmake  
  ${CMAKE_CURRENT_BINARY_DIR}/cmake/CPM.cmake  
  EXPECTED_HASH SHA256=cc155ce02e7945e7b8967ddfaff0b050e958a723ef7aad3766d368940cb15494  
)  
include(${CMAKE_CURRENT_BINARY_DIR}/cmake/CPM.cmake)  
  
set(Boost_DEBUG ON)  
  
CPMAddPackage(  
  NAME Boost  
  VERSION 1.83.0  
  GITHUB_REPOSITORY "boostorg/boost"  
  GIT_TAG "boost-1.83.0"  
)  
  
target_link_libraries(CPMExampleBoost PRIVATE Boost::headers)  
```  
  
```  
// Taken from https://www.boost.org/doc/libs/1_83_0/doc/html/accumulators/user_s_guide.html  
  
#include <boost/accumulators/accumulators.hpp>  
#include <boost/accumulators/statistics/mean.hpp>  
#include <boost/accumulators/statistics/moment.hpp>  
#include <boost/accumulators/statistics/stats.hpp>  
#include <iostream>  
using namespace boost::accumulators;  
  
int main() {  
  accumulator_set<double, stats<tag::mean, tag::moment<2>>> acc;  
  
  acc(1.2);  
  acc(2.3);  
  acc(3.4);  
  acc(4.5);  
  
  std::cout << "Mean:   " << mean(acc) << std::endl;  
  std::cout << "Moment: " << moment<2>(acc) << std::endl;  
}  
  
```  
  
main.cpp:4:10: fatal error: boost/accumulators/accumulators.hpp: No such file or directory  
    4 | #include <boost/accumulators/accumulators.hpp>

---

## Comment 1

> Username: pdimov  
> Created at: 2023-11-17 01:01:49 UTC  
> Url: https://github.com/boostorg/cmake/issues/45#issuecomment-1815570996  

When using Boost via `add_subdirectory` or `FetchContent`, and I suppose CPM, you have to link to the libraries you use even if they are header-only (`Boost::accumulators` in this case), `Boost::headers` doesn't work.

---

## Comment 2

> Username: lproj  
> Created at: 2023-11-17 08:53:59 UTC  
> Url: https://github.com/boostorg/cmake/issues/45#issuecomment-1815966205  

Thanks for your answer.  
I get this error when linking to `Boost::accumulators`:  
```  
  
CMake Error at CMakeLists.txt:5 (add_executable):  
  Target "CPMExampleBoost" links to target "Boost::numeric_ublas" but the  
  target was not found.  Perhaps a find_package() call is missing for an  
  IMPORTED target, or an ALIAS target is missing?  
```  
  
But I do not see Boost::numeric_ublas listed in the output of CMake:  
  
```  
-- CPM: Adding package Boost@1.83.0 (boost-1.83.0)  
-- Boost: using CMake 3.22.1  
-- Boost: Debug build, static libraries, MPI OFF, Python OFF, testing OFF  
-- Adding Boost library accumulators  
-- Enabling installation for 'accumulators'  
-- boost_install_target: not installing target 'boost_accumulators' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library algorithm  
-- Enabling installation for 'algorithm'  
-- boost_install_target: not installing target 'boost_algorithm' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library align  
-- Enabling installation for 'align'  
-- boost_install_target: not installing target 'boost_align' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library any  
-- Enabling installation for 'any'  
-- boost_install_target: not installing target 'boost_any' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library array  
-- Enabling installation for 'array'  
-- boost_install_target: not installing target 'boost_array' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library asio  
-- Enabling installation for 'asio'  
-- boost_install_target: not installing target 'boost_asio' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library assert  
-- Enabling installation for 'assert'  
-- boost_install_target: not installing target 'boost_assert' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library assign  
-- Enabling installation for 'assign'  
-- boost_install_target: not installing target 'boost_assign' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library atomic  
-- Enabling installation for 'atomic'  
-- boost_install_target: not installing target 'boost_atomic' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library beast  
-- Enabling installation for 'beast'  
-- boost_install_target: not installing target 'boost_beast' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library bimap  
-- Enabling installation for 'bimap'  
-- boost_install_target: not installing target 'boost_bimap' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library bind  
-- Enabling installation for 'bind'  
-- boost_install_target: not installing target 'boost_bind' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library callable_traits  
-- Enabling installation for 'callable_traits'  
-- boost_install_target: not installing target 'boost_callable_traits' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library chrono  
-- Enabling installation for 'chrono'  
-- boost_install_target: not installing target 'boost_chrono' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library circular_buffer  
-- Enabling installation for 'circular_buffer'  
-- boost_install_target: not installing target 'boost_circular_buffer' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library compat  
-- Enabling installation for 'compat'  
-- boost_install_target: not installing target 'boost_compat' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library compatibility  
-- Enabling installation for 'compatibility'  
-- boost_install_target: not installing target 'boost_compatibility' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library compute  
-- Enabling installation for 'compute'  
-- boost_install_target: not installing target 'boost_compute' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library concept_check  
-- Enabling installation for 'concept_check'  
-- boost_install_target: not installing target 'boost_concept_check' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library config  
-- Enabling installation for 'config'  
-- boost_install_target: not installing target 'boost_config' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library container  
-- Enabling installation for 'container'  
-- boost_install_target: not installing target 'boost_container' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library container_hash  
-- Enabling installation for 'container_hash'  
-- boost_install_target: not installing target 'boost_container_hash' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library context  
-- Boost.Context: architecture x86_64, binary format elf, ABI sysv, assembler gas, suffix .S, implementation fcontext  
-- Enabling installation for 'context'  
-- boost_install_target: not installing target 'boost_context' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library contract  
-- Enabling installation for 'contract'  
-- boost_install_target: not installing target 'boost_contract' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library conversion  
-- Enabling installation for 'conversion'  
-- boost_install_target: not installing target 'boost_conversion' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library convert  
-- Enabling installation for 'convert'  
-- boost_install_target: not installing target 'boost_convert' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library core  
-- Enabling installation for 'core'  
-- boost_install_target: not installing target 'boost_core' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library coroutine  
-- Enabling installation for 'coroutine'  
-- boost_install_target: not installing target 'boost_coroutine' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library coroutine2  
-- Enabling installation for 'coroutine2'  
-- boost_install_target: not installing target 'boost_coroutine2' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library crc  
-- Enabling installation for 'crc'  
-- boost_install_target: not installing target 'boost_crc' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library date_time  
-- Enabling installation for 'date_time'  
-- boost_install_target: not installing target 'boost_date_time' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library describe  
-- Enabling installation for 'describe'  
-- boost_install_target: not installing target 'boost_describe' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library detail  
-- Enabling installation for 'detail'  
-- boost_install_target: not installing target 'boost_detail' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library dll  
-- Enabling installation for 'dll'  
-- boost_install_target: not installing target 'boost_dll' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library dynamic_bitset  
-- Enabling installation for 'dynamic_bitset'  
-- boost_install_target: not installing target 'boost_dynamic_bitset' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library endian  
-- Enabling installation for 'endian'  
-- boost_install_target: not installing target 'boost_endian' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library exception  
-- Enabling installation for 'exception'  
-- boost_install_target: not installing target 'boost_exception' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library fiber  
-- Boost.Fiber: NUMA target OS is linux  
-- boost_install_target: not installing target 'boost_fiber' due to BOOST_SKIP_INSTALL_RULES=ON  
-- boost_install_target: not installing target 'boost_fiber_numa' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Enabling installation for 'fiber'  
-- boost_install_target: not installing target 'boost_fiber' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library filesystem  
-- Enabling installation for 'filesystem'  
-- boost_install_target: not installing target 'boost_filesystem' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library flyweight  
-- Enabling installation for 'flyweight'  
-- boost_install_target: not installing target 'boost_flyweight' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library foreach  
-- Enabling installation for 'foreach'  
-- boost_install_target: not installing target 'boost_foreach' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library format  
-- Enabling installation for 'format'  
-- boost_install_target: not installing target 'boost_format' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library function  
-- Enabling installation for 'function'  
-- boost_install_target: not installing target 'boost_function' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library function_types  
-- Enabling installation for 'function_types'  
-- boost_install_target: not installing target 'boost_function_types' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library functional  
-- Enabling installation for 'functional'  
-- boost_install_target: not installing target 'boost_functional' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library fusion  
-- Enabling installation for 'fusion'  
-- boost_install_target: not installing target 'boost_fusion' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library geometry  
-- Enabling installation for 'geometry'  
-- boost_install_target: not installing target 'boost_geometry' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library gil  
-- Enabling installation for 'gil'  
-- boost_install_target: not installing target 'boost_gil' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library graph  
-- Enabling installation for 'graph'  
-- boost_install_target: not installing target 'boost_graph' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Skipping Boost library graph_parallel, BOOST_ENABLE_MPI is OFF  
-- Adding Boost library hana  
-- Enabling installation for 'hana'  
-- boost_install_target: not installing target 'boost_hana' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library headers  
-- Enabling installation for 'headers'  
-- boost_install_target: not installing target 'boost_headers' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library heap  
-- Enabling installation for 'heap'  
-- boost_install_target: not installing target 'boost_heap' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library histogram  
-- Enabling installation for 'histogram'  
-- boost_install_target: not installing target 'boost_histogram' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library hof  
-- Enabling installation for 'hof'  
-- boost_install_target: not installing target 'boost_hof' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library icl  
-- Enabling installation for 'icl'  
-- boost_install_target: not installing target 'boost_icl' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library integer  
-- Enabling installation for 'integer'  
-- boost_install_target: not installing target 'boost_integer' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library interprocess  
-- Enabling installation for 'interprocess'  
-- boost_install_target: not installing target 'boost_interprocess' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library intrusive  
-- Enabling installation for 'intrusive'  
-- boost_install_target: not installing target 'boost_intrusive' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library io  
-- Enabling installation for 'io'  
-- boost_install_target: not installing target 'boost_io' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library iostreams  
-- Boost.Iostreams: ZLIB ON, BZip2 OFF, LZMA OFF, Zstd OFF  
-- Enabling installation for 'iostreams'  
-- boost_install_target: not installing target 'boost_iostreams' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library iterator  
-- Enabling installation for 'iterator'  
-- boost_install_target: not installing target 'boost_iterator' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library json  
-- Enabling installation for 'json'  
-- boost_install_target: not installing target 'boost_json' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library lambda  
-- Enabling installation for 'lambda'  
-- boost_install_target: not installing target 'boost_lambda' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library lambda2  
-- Enabling installation for 'lambda2'  
-- boost_install_target: not installing target 'boost_lambda2' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library leaf  
-- Enabling installation for 'leaf'  
-- boost_install_target: not installing target 'boost_leaf' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library lexical_cast  
-- Enabling installation for 'lexical_cast'  
-- boost_install_target: not installing target 'boost_lexical_cast' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library local_function  
-- Enabling installation for 'local_function'  
-- boost_install_target: not installing target 'boost_local_function' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library locale  
-- Found the following ICU libraries:  
--   data (required)  
--   i18n (required)  
--   uc (required)  
-- Boost.Locale: iconv ON, ICU ON, POSIX ON, std ON, winapi OFF  
-- Enabling installation for 'locale'  
-- boost_install_target: not installing target 'boost_locale' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library lockfree  
-- Enabling installation for 'lockfree'  
-- boost_install_target: not installing target 'boost_lockfree' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library log  
-- boost_install_target: not installing target 'boost_log' due to BOOST_SKIP_INSTALL_RULES=ON  
-- boost_install_target: not installing target 'boost_log_setup' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Enabling installation for 'log'  
-- boost_install_target: not installing target 'boost_log' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library logic  
-- Enabling installation for 'logic'  
-- boost_install_target: not installing target 'boost_logic' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library math  
-- Boost.Math: standalone mode OFF  
-- Enabling installation for 'math'  
-- boost_install_target: not installing target 'boost_math' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library metaparse  
-- Enabling installation for 'metaparse'  
-- boost_install_target: not installing target 'boost_metaparse' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library move  
-- Enabling installation for 'move'  
-- boost_install_target: not installing target 'boost_move' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library mp11  
-- Enabling installation for 'mp11'  
-- boost_install_target: not installing target 'boost_mp11' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Skipping Boost library mpi, BOOST_ENABLE_MPI is OFF  
-- Adding Boost library mpl  
-- Enabling installation for 'mpl'  
-- boost_install_target: not installing target 'boost_mpl' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library msm  
-- Enabling installation for 'msm'  
-- boost_install_target: not installing target 'boost_msm' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library multi_array  
-- Enabling installation for 'multi_array'  
-- boost_install_target: not installing target 'boost_multi_array' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library multi_index  
-- Enabling installation for 'multi_index'  
-- boost_install_target: not installing target 'boost_multi_index' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library multiprecision  
-- Boost.Multiprecision: standalone mode OFF  
-- Enabling installation for 'multiprecision'  
-- boost_install_target: not installing target 'boost_multiprecision' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library mysql  
-- Enabling installation for 'mysql'  
-- boost_install_target: not installing target 'boost_mysql' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library nowide  
-- Enabling installation for 'nowide'  
-- boost_install_target: not installing target 'boost_nowide' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library numeric/conversion  
-- Enabling installation for 'numeric/conversion'  
-- boost_install_target: not installing target 'boost_numeric_conversion' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library numeric/interval  
-- Enabling installation for 'numeric/interval'  
-- boost_install_target: not installing target 'boost_numeric_interval' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library optional  
-- Enabling installation for 'optional'  
-- boost_install_target: not installing target 'boost_optional' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library outcome  
-- Enabling installation for 'outcome'  
-- boost_install_target: not installing target 'boost_outcome' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library parameter  
-- Enabling installation for 'parameter'  
-- boost_install_target: not installing target 'boost_parameter' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Skipping Boost library parameter_python, BOOST_ENABLE_PYTHON is OFF  
-- Adding Boost library pfr  
-- Enabling installation for 'pfr'  
-- boost_install_target: not installing target 'boost_pfr' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library phoenix  
-- Enabling installation for 'phoenix'  
-- boost_install_target: not installing target 'boost_phoenix' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library poly_collection  
-- Enabling installation for 'poly_collection'  
-- boost_install_target: not installing target 'boost_poly_collection' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library polygon  
-- Enabling installation for 'polygon'  
-- boost_install_target: not installing target 'boost_polygon' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library pool  
-- Enabling installation for 'pool'  
-- boost_install_target: not installing target 'boost_pool' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library predef  
-- Enabling installation for 'predef'  
-- boost_install_target: not installing target 'boost_predef' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library preprocessor  
-- Enabling installation for 'preprocessor'  
-- boost_install_target: not installing target 'boost_preprocessor' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library process  
-- Enabling installation for 'process'  
-- boost_install_target: not installing target 'boost_process' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library program_options  
-- Enabling installation for 'program_options'  
-- boost_install_target: not installing target 'boost_program_options' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library property_map  
-- Enabling installation for 'property_map'  
-- boost_install_target: not installing target 'boost_property_map' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Skipping Boost library property_map_parallel, BOOST_ENABLE_MPI is OFF  
-- Adding Boost library property_tree  
-- Enabling installation for 'property_tree'  
-- boost_install_target: not installing target 'boost_property_tree' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library proto  
-- Enabling installation for 'proto'  
-- boost_install_target: not installing target 'boost_proto' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library ptr_container  
-- Enabling installation for 'ptr_container'  
-- boost_install_target: not installing target 'boost_ptr_container' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Skipping Boost library python, BOOST_ENABLE_PYTHON is OFF  
-- Adding Boost library qvm  
-- Enabling installation for 'qvm'  
-- boost_install_target: not installing target 'boost_qvm' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library random  
-- Enabling installation for 'random'  
-- boost_install_target: not installing target 'boost_random' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library range  
-- Enabling installation for 'range'  
-- boost_install_target: not installing target 'boost_range' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library ratio  
-- Enabling installation for 'ratio'  
-- boost_install_target: not installing target 'boost_ratio' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library rational  
-- Enabling installation for 'rational'  
-- boost_install_target: not installing target 'boost_rational' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library regex  
-- Found the following ICU libraries:  
--   data (required)  
--   i18n (required)  
--   uc (required)  
-- Enabling installation for 'regex'  
-- boost_install_target: not installing target 'boost_regex' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library safe_numerics  
-- Enabling installation for 'safe_numerics'  
-- boost_install_target: not installing target 'boost_safe_numerics' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library scope_exit  
-- Enabling installation for 'scope_exit'  
-- boost_install_target: not installing target 'boost_scope_exit' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library serialization  
-- boost_install_target: not installing target 'boost_serialization' due to BOOST_SKIP_INSTALL_RULES=ON  
-- boost_install_target: not installing target 'boost_wserialization' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Enabling installation for 'serialization'  
-- boost_install_target: not installing target 'boost_serialization' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library signals2  
-- Enabling installation for 'signals2'  
-- boost_install_target: not installing target 'boost_signals2' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library smart_ptr  
-- Enabling installation for 'smart_ptr'  
-- boost_install_target: not installing target 'boost_smart_ptr' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library sort  
-- Enabling installation for 'sort'  
-- boost_install_target: not installing target 'boost_sort' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library spirit  
-- Enabling installation for 'spirit'  
-- boost_install_target: not installing target 'boost_spirit' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library stacktrace  
-- Boost.Stacktrace: noop ON, backtrace ON, addr2line ON, basic ON, windbg OFF, windbg_cached OFF  
-- boost_install_target: not installing target 'boost_stacktrace_noop' due to BOOST_SKIP_INSTALL_RULES=ON  
-- boost_install_target: not installing target 'boost_stacktrace_backtrace' due to BOOST_SKIP_INSTALL_RULES=ON  
-- boost_install_target: not installing target 'boost_stacktrace_addr2line' due to BOOST_SKIP_INSTALL_RULES=ON  
-- boost_install_target: not installing target 'boost_stacktrace_basic' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Not enabling installation for 'stacktrace'; targets 'Boost::stacktrace' and 'boost_stacktrace' weren't found  
-- Adding Boost library statechart  
-- Enabling installation for 'statechart'  
-- boost_install_target: not installing target 'boost_statechart' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library static_assert  
-- Enabling installation for 'static_assert'  
-- boost_install_target: not installing target 'boost_static_assert' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library static_string  
-- Enabling installation for 'static_string'  
-- boost_install_target: not installing target 'boost_static_string' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library stl_interfaces  
-- Enabling installation for 'stl_interfaces'  
-- boost_install_target: not installing target 'boost_stl_interfaces' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library system  
-- Enabling installation for 'system'  
-- boost_install_target: not installing target 'boost_system' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library test  
-- boost_install_target: not installing target 'boost_prg_exec_monitor' due to BOOST_SKIP_INSTALL_RULES=ON  
-- boost_install_target: not installing target 'boost_test_exec_monitor' due to BOOST_SKIP_INSTALL_RULES=ON  
-- boost_install_target: not installing target 'boost_unit_test_framework' due to BOOST_SKIP_INSTALL_RULES=ON  
-- boost_install_target: not installing target 'boost_included_prg_exec_monitor' due to BOOST_SKIP_INSTALL_RULES=ON  
-- boost_install_target: not installing target 'boost_included_test_exec_monitor' due to BOOST_SKIP_INSTALL_RULES=ON  
-- boost_install_target: not installing target 'boost_included_unit_test_framework' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Not enabling installation for 'test'; targets 'Boost::test' and 'boost_test' weren't found  
-- Adding Boost library thread  
-- Boost.Thread: threading API is pthread  
-- Enabling installation for 'thread'  
-- boost_install_target: not installing target 'boost_thread' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library throw_exception  
-- Enabling installation for 'throw_exception'  
-- boost_install_target: not installing target 'boost_throw_exception' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library timer  
-- Enabling installation for 'timer'  
-- boost_install_target: not installing target 'boost_timer' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library tokenizer  
-- Enabling installation for 'tokenizer'  
-- boost_install_target: not installing target 'boost_tokenizer' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library tti  
-- Enabling installation for 'tti'  
-- boost_install_target: not installing target 'boost_tti' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library tuple  
-- Enabling installation for 'tuple'  
-- boost_install_target: not installing target 'boost_tuple' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library type_erasure  
-- Enabling installation for 'type_erasure'  
-- boost_install_target: not installing target 'boost_type_erasure' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library type_index  
-- Enabling installation for 'type_index'  
-- boost_install_target: not installing target 'boost_type_index' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library type_traits  
-- Enabling installation for 'type_traits'  
-- boost_install_target: not installing target 'boost_type_traits' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library typeof  
-- Enabling installation for 'typeof'  
-- boost_install_target: not installing target 'boost_typeof' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library units  
-- Enabling installation for 'units'  
-- boost_install_target: not installing target 'boost_units' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library unordered  
-- Enabling installation for 'unordered'  
-- boost_install_target: not installing target 'boost_unordered' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library url  
-- Enabling installation for 'url'  
-- boost_install_target: not installing target 'boost_url' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library utility  
-- Enabling installation for 'utility'  
-- boost_install_target: not installing target 'boost_utility' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library uuid  
-- Enabling installation for 'uuid'  
-- boost_install_target: not installing target 'boost_uuid' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library variant  
-- Enabling installation for 'variant'  
-- boost_install_target: not installing target 'boost_variant' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library variant2  
-- Enabling installation for 'variant2'  
-- boost_install_target: not installing target 'boost_variant2' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library vmd  
-- Enabling installation for 'vmd'  
-- boost_install_target: not installing target 'boost_vmd' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library wave  
-- Enabling installation for 'wave'  
-- boost_install_target: not installing target 'boost_wave' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library winapi  
-- Enabling installation for 'winapi'  
-- boost_install_target: not installing target 'boost_winapi' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library xpressive  
-- Enabling installation for 'xpressive'  
-- boost_install_target: not installing target 'boost_xpressive' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Adding Boost library yap  
-- Enabling installation for 'yap'  
-- boost_install_target: not installing target 'boost_yap' due to BOOST_SKIP_INSTALL_RULES=ON  
-- Boost: not installing BoostConfig.cmake due to BOOST_SKIP_INSTALL_RULES=ON  
  
  
  
```

---

## Comment 3

> Username: pdimov  
> Created at: 2023-11-17 11:48:37 UTC  
> Url: https://github.com/boostorg/cmake/issues/45#issuecomment-1816248640  

That's because the maintainers of uBLAS are sitting on https://github.com/boostorg/ublas/pull/142 for years now without merging it, for some reason.  
  
I'll see what I can do to have this fixed. In the meantime, if CPM supports applying a patch, you can use the one from the PR above to add the needed CMakeLists.txt file to `libs/numeric/ublas`.

---

## Comment 4

> Username: lproj  
> Created at: 2023-11-17 17:46:51 UTC  
> Url: https://github.com/boostorg/cmake/issues/45#issuecomment-1816842193  

Many thanks for your support!  
  
Are you aware of any other libraries in the same situation as accumulators? The one above was just an example, but we are going to use CMake for linking to many, many other boost libraries in our projects.  
  
Thanks.

---

## Comment 5

> Username: pdimov  
> Created at: 2023-11-17 17:52:08 UTC  
> Url: https://github.com/boostorg/cmake/issues/45#issuecomment-1816848650  

The two libraries that depend on uBLAS are Accumulators and ODEint: https://pdimov.github.io/boostdep-report/master/numeric~ublas.html#reverse-dependencies  
  
Everything else should work.

---

## Comment 6

> Username: nigels-com  
> Created at: 2025-12-14 08:22:00 UTC  
> Url: https://github.com/boostorg/cmake/issues/45#issuecomment-3650469324  

@lproj Is this still an issue with more recent boost releases?
