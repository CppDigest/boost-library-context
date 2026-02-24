# #555 - Missing CMake dependency [Closed]

> Username: pdimov  
> Created at: 2021-05-02 17:46:47 UTC  
> Updated at: 2021-05-06 15:57:50 UTC  
> Closed at: 2021-05-06 15:57:50 UTC  
> Url: https://github.com/boostorg/json/issues/555  

https://travis-ci.org/github/boostorg/boost/jobs/769225032#L1226  
```  
Scanning dependencies of target boost_json  
[ 31%] Building CXX object libs/json/CMakeFiles/boost_json.dir/src/src.cpp.o  
In file included from /home/travis/build/boostorg/boost/libs/json/include/boost/json/detail/value_to.hpp:16:0,  
                 from /home/travis/build/boostorg/boost/libs/json/include/boost/json/value_to.hpp:16,  
                 from /home/travis/build/boostorg/boost/libs/json/include/boost/json.hpp:40,  
                 from /home/travis/build/boostorg/boost/libs/json/include/boost/json/src.hpp:27,  
                 from /home/travis/build/boostorg/boost/libs/json/src/src.cpp:10:  
/home/travis/build/boostorg/boost/libs/json/include/boost/json/detail/index_sequence.hpp:15:44: fatal error: boost/mp11/integer_sequence.hpp: No such file or directory  
```  
Likely caused by `Boost::mp11` missing in the list of dependencies here: https://github.com/boostorg/json/blob/aae1863def3fb605d77ebbefc851cc26ac1758a9/CMakeLists.txt#L80-L89  
  
`boostdep --cmake json` says  
```  
target_link_libraries(boost_json  
  PUBLIC  
    Boost::align  
    Boost::assert  
    Boost::config  
    Boost::container  
    Boost::exception  
    Boost::mp11  
    Boost::system  
    Boost::throw_exception  
    Boost::utility  
)  
```
