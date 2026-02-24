# #86 - boost_program_options failed to link due to undefined references [Open]

> Username: A13XIS  
> Created at: 2019-10-25 19:24:03 UTC  
> Updated at: 2022-03-29 03:32:49 UTC  
> Url: https://github.com/boostorg/program_options/issues/86  

When building a program that uses boost::program_options a couple of undefined refereneces show up:  
```  
CMakeFiles/saryxo_train.dir/src/main.cpp.o: In Function »boost::program_options::basic_command_line_parser<char>::basic_command_line_parser(int, char const* const*)«:  
/usr/local/include/boost/program_options/detail/parsers.hpp:28: Warning: undefined reference to »boost::program_options::detail::cmdline::cmdline(std::__debug::vector<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > const&)«  
CMakeFiles/saryxo_train.dir/src/main.cpp.o:(.data.rel.ro._ZTVN5boost15program_options11typed_valueIicEE[_ZTVN5boost15program_options11typed_valueIicEE]+0x38): Warning: undefined reference to »boost::program_options::value_semantic_codecvt_helper<char>::parse(boost::any&, std::__debug::vector<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > const&, bool) const«  
CMakeFiles/saryxo_train.dir/src/main.cpp.o:(.data.rel.ro._ZTVN5boost15program_options11typed_valueIjcEE[_ZTVN5boost15program_options11typed_valueIjcEE]+0x38): Warning: undefined reference to »boost::program_options::value_semantic_codecvt_helper<char>::parse(boost::any&, std::__debug::vector<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > const&, bool) const«  
CMakeFiles/saryxo_train.dir/src/main.cpp.o:(.data.rel.ro._ZTVN5boost15program_options11typed_valueINSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEcEE[_ZTVN5boost15program_options11typed_valueINSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEcEE]+0x38): Warning: undefined reference to »boost::program_options::value_semantic_codecvt_helper<char>::parse(boost::any&, std::__debug::vector<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > const&, bool) const«  
CMakeFiles/saryxo_train.dir/src/main.cpp.o: In Function »boost::program_options::typed_value<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, char>::xparse(boost::any&, std::__debug::vector<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > const&) const«:  
/usr/local/include/boost/program_options/detail/value_semantic.hpp:184: Warning: undefined reference to  »boost::program_options::validate(boost::any&, std::__debug::vector<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > const&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >*, int)«  
```  
  
I am using CMake 3.14 and link boost in the CMake list like:  
```  
cmake_minimum_required(VERSION 3.14)  
set (CMAKE_CXX_STANDARD 14)  
find_package(Boost 1.70 COMPONENTS filesystem program_options REQUIRED)  
...  
target_link_libraries(project ${Boost_LIBRARIES})  
```  
  
*System Plattform: Ubuntu x86_64  
CMake/GCC Version: 3.14/7.4.0  
Boost Version: 1.71*

---

## Comment 1

> Username: vprus  
> Created at: 2019-10-28 15:38:12 UTC  
> Url: https://github.com/boostorg/program_options/issues/86#issuecomment-547005795  

What is the exact command line used to link your program? CMake should have a way to output it.  
  
From the above messages, it seems like rpath-link or similar options are not correct.

---

## Comment 2

> Username: westsail42  
> Created at: 2022-03-29 02:40:16 UTC  
> Url: https://github.com/boostorg/program_options/issues/86#issuecomment-1081340398  

Ran into this today with the 1.78.0 conan package.   
  
```  
            _options_desc.add_options()  
...  
;  
  
            auto c = po::parse_command_line(argc, argv, _options_desc);  // <---- errors this line  
            po::store(c , _options_map);  
            po::notify(_options_map);  
  
```  
The Error:  
  
```  
../lib/libsdc_cpp.a(PPCServer.cpp.o): In function `boost::program_options::basic_command_line_parser<char>::basic_command_line_parser(int, char const* const*)':  
/home/rob/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/program_options/detail/parsers.hpp:28: undefined reference to `boost::program_options::detail::cmdline::cmdline(std::__debug::vector<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > const&)'  
  
```  
  
This info from Conan may be useful  
  
  
```  
[full_settings]  
    arch=x86_64  
    arch_build=x86_64  
    build_type=Release  
    compiler=gcc  
    compiler.libcxx=libstdc++11  
    compiler.version=7  
    os=Linux  
    os_build=Linux  
```

---

## Comment 3

> Username: westsail42  
> Created at: 2022-03-29 03:32:49 UTC  
> Url: https://github.com/boostorg/program_options/issues/86#issuecomment-1081364708  

Additional. Looking to use the same compiler/settings in search of a solution, the following CMake ExternalProject declaration will download and build from source. Link still fails with "undefined reference"  
```  
  
SET(BOOST_CONFIGURE <SOURCE_DIR>/bootstrap.sh --prefix=<SOURCE_DIR>)  
SET(BOOST_INSTALL <SOURCE_DIR>/b2 install --prefix=<BINARY_DIR>/../build)  
ExternalProject_Add(boost  
        GIT_REPOSITORY https://github.com/boostorg/boost.git  
        GIT_TAG        boost-1.78.0  
        PREFIX         ${CMAKE_CURRENT_BINARY_DIR}/_deps/boost  
        # Because when executing b2, you need to read boost build. Exe in the same directory Jam, so BUILD_IN_SOURCE  
        BUILD_IN_SOURCE   true  
        CONFIGURE_COMMAND "${BOOST_CONFIGURE}"  
        BUILD_COMMAND       ""  
        INSTALL_COMMAND   "${BOOST_INSTALL}"  
        )  
set(Boost_INCLUDE_DIRS ${CMAKE_CURRENT_BINARY_DIR}/_deps/boost/src/build/include)  
set(Boost_LIBRARIES ${CMAKE_CURRENT_BINARY_DIR}/_deps/boost/src/build/lib/libboost_program_options.a)  
```
