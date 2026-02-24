# #215 - Log Formatting [Conan 2.x, C++20, Boost 1.83] [Closed]

> Username: matheusdiogenesandrade  
> Created at: 2023-08-28 18:20:37 UTC  
> Updated at: 2023-08-29 08:05:29 UTC  
> Closed at: 2023-08-28 20:11:54 UTC  
> Url: https://github.com/boostorg/log/issues/215  

**Environment**  
  
- Linux pop-os x86_64 GNU/Linux  
- Conan 2.0.9  
- CMake 3.25.1  
- Boost 1.83.0  
- C++20  
  
**Current structure**  
  
The MWE can be found [here](https://github.com/matheusdiogenesandrade/Boost_conan_test).  
  
- `conanprofile.txt`:  
  
```conan  
[settings]  
os=Linux  
arch=x86_64  
compiler=gcc  
compiler.version=11  
compiler.cppstd=20  
compiler.libcxx=libstdc++11  
build_type=Release  
  
[options]  
  
[conf]  
```  
  
- `conanfile.txt`:  
  
```conan  
[requires]  
boost/1.83.0  
  
[generators]  
CMakeDeps  
CMakeToolchain  
```  
  
- `CMakeLists.txt`:  
  
```cmake  
cmake_minimum_required(VERSION 3.23)  
project(BoostConan)  
  
##### SETs  
set (CMAKE_BUILD_TYPE Release)   
set (CMAKE_CXX_STANDARD 20)  
set (BUILD_SHARED_LIBS On)  
  
##### Additional packages   
  
find_package (Boost COMPONENTS log REQUIRED)  
  
##### Include Dirs   
  
include_directories(  
    SYSTEM  
    includes  
    "${PROJECT_BINARY_DIR}"  
    )  
  
##### Compile options   
  
add_compile_options(-Wall  
    -DIL_STD  
    -Wfatal-errors  
    -pedantic  
    -fopenmp  
    -DDEBUG  
    -fdiagnostics-color=always  
    -lz  
    -std=c++20  
    -lm   
    -lpthread   
    -ldl  
    -Wno-sign-compare  
    )  
  
file(GLOB_RECURSE SOURCES "src/*.cpp")  
  
add_executable(main ${SOURCES})  
  
target_link_libraries(  
    main   
    Boost::log  
    pthread   
    m   
    dl)  
```  
  
- `src/main.cpp`:  
  
```cpp  
#include <boost/log/core.hpp>  
#include <boost/log/trivial.hpp>  
#include <boost/log/expressions.hpp>  
#include <boost/log/utility/setup/file.hpp>  
#include <boost/log/utility/setup/common_attributes.hpp>  
  
namespace logging = boost::log;  
namespace keywords = boost::log::keywords;  
  
void init_logging() {  
    logging::register_simple_formatter_factory<logging::trivial::severity_level, char>("Severity");  
  
    logging::add_file_log(  
            keywords::file_name = "sample.log",  
            keywords::auto_flush = true,  
            keywords::format = "[%TimeStamp%] [%ThreadID%] [%Severity%] [%ProcessID%] [%LineID%] %Message%"  
            );  
  
   logging::add_common_attributes();  
}  
  
int main (int argc, const char *argv[])  
{  
    init_logging();  
  
    BOOST_LOG_TRIVIAL(debug) << "Test";  
    return 0;  
}  
```  
  
**Process**  
  
```sh  
mkdir build  
conan install . --profile conanprofile.txt --output-folder=build --build=missing  
cd build  
cmake .. --preset conan-release  
make  
```  
  
**Expected result**  
  
```sh  
[ 50%] Building CXX object CMakeFiles/main.dir/src/main.cpp.o  
[100%] Linking CXX executable main  
[100%] Built target main  
```  
  
**Actual result**  
  
```sh  
/usr/bin/ld: CMakeFiles/main.dir/src/main.cpp.o: en la función `init_logging()':   
main.cpp:(.text+0x186): reference to   
`void boost::log::v2s_mt_posix::register_formatter_factory<char>(  
boost::log::v2s_mt_posix::attribute_name const&, boost::shared_ptr<boost::log::v2s_mt_posix::formatter_factory<char> > const&  
)' not defined  
  
/usr/bin/ld: CMakeFiles/main.dir/src/main.cpp.o: in the function  
`boost::shared_ptr<boost::log::v2s_mt_posix::sinks::synchronous_sink<boost::log::v2s_mt_posix::sinks::text_file_backend> >  
boost::log::v2s_mt_posix::aux::add_file_log<boost::parameter::aux::flat_like_arg_list<boost::parameter::aux::flat_like_arg_tuple<boost::log::v2s_mt_posix::keywords::tag::file_name,  
boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::file_name, char const [11]>,  
std::integral_constant<bool, true> >,   
boost::parameter::aux::flat_like_arg_tuple<boost::log::v2s_mt_posix::keywords::tag::auto_flush,   
boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::auto_flush, bool const>,   
std::integral_constant<bool, true> >,   
boost::parameter::aux::flat_like_arg_tuple<boost::log::v2s_mt_posix::keywords::tag::format,   
boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::format, char const [75]>,   
std::integral_constant<bool, true> > > >  
(boost::parameter::aux::flat_like_arg_list<boost::parameter::aux::flat_like_arg_tuple<boost::log::v2s_mt_posix::keywords::tag::file_name, boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::file_name, char const [11]>,   
std::integral_constant<bool, true> >,   
boost::parameter::aux::flat_like_arg_tuple<boost::log::v2s_mt_posix::keywords::tag::auto_flush,   
boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::auto_flush, bool const>,   
std::integral_constant<bool, true> >,   
boost::parameter::aux::flat_like_arg_tuple<boost::log::v2s_mt_posix::keywords::tag::format,   
boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::format, char const [75]>,   
std::integral_constant<bool, true> > > const&)':  
  
main.cpp:(.text._ZN5boost3log12v2s_mt_posix3aux12add_file_logINS_9parameter3aux18flat_like_arg_listIJNS5_19flat_like_arg_tupleINS1_8keywords3tag9file_nameENS5_15tagged_argumentISA_A11_KcEESt17integral_constantIbLb1EEEENS7_INS9_10auto_flushENSB_ISI_KbEESG_EENS7_INS9_6formatENSB_ISM_A75_SC_EESG_EEEEEEENS_10shared_ptrINS1_5sinks16synchronous_sinkINSS_17text_file_backendEEEEERKT_[_ZN5boost3log12v2s_mt_posix3aux12add_file_logINS_9parameter3aux18flat_like_arg_listIJNS5_19flat_like_arg_tupleINS1_8keywords3tag9file_nameENS5_15tagged_argumentISA_A11_KcEESt17integral_constantIbLb1EEEENS7_INS9_10auto_flushENSB_ISI_KbEESG_EENS7_INS9_6formatENSB_ISM_A75_SC_EESG_EEEEEEENS_10shared_ptrINS1_5sinks16synchronous_sinkINSS_17text_file_backendEEEEERKT_]+0x5b):   
reference to   
`boost::log::v2s_mt_posix::basic_formatter<char> boost::log::v2s_mt_posix::parse_formatter<char>(char const*, char const*)'   
not defined  
collect2: error: ld returned 1 exit status  
make[2]: *** [CMakeFiles/main.dir/build.make:107: main] Error 1  
make[1]: *** [CMakeFiles/Makefile2:83: CMakeFiles/main.dir/all] Error 2  
make: *** [Makefile:91: all] Error 2  
```  
  
**Goal**  
  
Fix the compilation without changing the syntax `BOOST_LOG_TRIVIAL(debug) << "Test";`.  
  
Thanks and regards.

---

## Comment 1

> Username: Lastique  
> Created at: 2023-08-28 20:11:54 UTC  
> Url: https://github.com/boostorg/log/issues/215#issuecomment-1696344994  

You're missing `Boost::log_setup` in your `target_link_libraries`.

---

## Comment 2

> Username: matheusdiogenesandrade  
> Created at: 2023-08-29 08:05:28 UTC  
> Url: https://github.com/boostorg/log/issues/215#issuecomment-1696964711  

Thank you very much.
