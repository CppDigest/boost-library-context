# #74 - Fail to use FindBoost [Closed]

> Username: zjyhjqs  
> Created at: 2024-10-02 15:55:31 UTC  
> Updated at: 2024-10-02 16:36:30 UTC  
> Closed at: 2024-10-02 16:36:30 UTC  
> Url: https://github.com/boostorg/cmake/issues/74  

Environment: CMake 3.30, windows x64  
  
I installed boost with options (just need header-only part):  
- `BOOST_ENABLE_COMPATIBILITY_TARGETS`: true  
- `BOOST_EXCLUDE_LIBRARIES`: "atomic;charconv;chrono;cobalt;container;context;contract;coroutine;date_time;exception;fiber;filesystem;graph;graph_parallel;iostreams;json;locale;log;math;mpi;mysql;nowide;process;program_options;python;random;regex;serialization;stacktrace;system;test;thread;timer;type_erasure;url;wave;predef"  
  
Then I tried to use it in my project (having set the `Boost_ROOT`):  
  
```cmake  
cmake_minimum_required(VERSION 3.30)  
project(CMakeTest LANGUAGES CXX)  
  
find_package(Boost REQUIRED)  
```  
  
The error message:  
  
```log  
CMake Error at X:/Boost-1.86.0/lib/cmake/Boost-1.86.0/BoostConfig.cmake:67 (find_package):  
  Found package configuration file:  
  
    X:/Boost-1.86.0/lib/cmake/boost_headers-1.86.0/boost_headers-config.cmake  
  
  but it set boost_headers_FOUND to FALSE so package "boost_headers" is  
  considered to be NOT FOUND.  Reason given by package:  
  
  The following imported targets are referenced, but are missing:  
  Boost::accumulators Boost::algorithm Boost::align Boost::any Boost::array  
  Boost::asio Boost::assert Boost::assign Boost::beast Boost::bimap  
  Boost::bind Boost::callable_traits Boost::circular_buffer Boost::compat  
  Boost::compatibility Boost::compute Boost::concept_check Boost::config  
  Boost::container_hash Boost::conversion Boost::convert Boost::core  
  Boost::coroutine2 Boost::crc Boost::describe Boost::detail Boost::dll  
  Boost::dynamic_bitset Boost::endian Boost::flyweight Boost::foreach  
  Boost::format Boost::function Boost::function_types Boost::functional  
  Boost::fusion Boost::geometry Boost::gil Boost::hana Boost::heap  
  Boost::histogram Boost::hof Boost::icl Boost::integer Boost::interprocess  
  Boost::intrusive Boost::io Boost::iterator Boost::lambda Boost::lambda2  
  Boost::leaf Boost::lexical_cast Boost::local_function Boost::lockfree  
  Boost::logic Boost::metaparse Boost::move Boost::mp11 Boost::mpl Boost::msm  
  Boost::multi_array Boost::multi_index Boost::multiprecision Boost::optional  
  Boost::outcome Boost::parameter Boost::pfr Boost::phoenix  
  Boost::poly_collection Boost::polygon Boost::pool Boost::preprocessor  
  Boost::property_map Boost::property_tree Boost::proto Boost::ptr_container  
  Boost::qvm Boost::range Boost::ratio Boost::rational Boost::redis  
  Boost::safe_numerics Boost::scope Boost::scope_exit Boost::signals2  
  Boost::smart_ptr Boost::sort Boost::spirit Boost::statechart  
  Boost::static_assert Boost::static_string Boost::stl_interfaces  
  Boost::throw_exception Boost::tokenizer Boost::tti Boost::tuple  
  Boost::type_index Boost::type_traits Boost::typeof Boost::units  
  Boost::unordered Boost::utility Boost::uuid Boost::variant Boost::variant2  
  Boost::vmd Boost::winapi Boost::xpressive Boost::yap  
  
Call Stack (most recent call first):  
  X:/Boost-1.86.0/lib/cmake/Boost-1.86.0/BoostConfig.cmake:97 (boostcfg_find_component)  
  CMakeLists.txt:4 (find_package)  
```  
  
---  
  
Seems the dependencies of `Boost::headers` haven't been imported yet.  
https://github.com/boostorg/cmake/blob/bb741d09d2cb12f3934896880c6ecaf6de4ba707/config/BoostConfig.cmake#L97

---

## Comment 1

> Username: pdimov  
> Created at: 2024-10-02 16:14:29 UTC  
> Url: https://github.com/boostorg/cmake/issues/74#issuecomment-2389074013  

You shouldn't set BOOST_ENABLE_COMPATIBILITY_TARGETS when installing; it's only needed for add_subdirectory/FetchContent use.

---

## Comment 2

> Username: zjyhjqs  
> Created at: 2024-10-02 16:36:30 UTC  
> Url: https://github.com/boostorg/cmake/issues/74#issuecomment-2389117268  

OK, it's working.  
  
May `BOOST_ENABLE_COMPATIBILITY_TARGETS ` should be documented.
