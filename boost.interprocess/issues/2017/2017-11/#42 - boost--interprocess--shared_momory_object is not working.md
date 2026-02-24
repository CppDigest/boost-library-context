# #42 - boost::interprocess::shared_momory_object is not working [Closed]

> Username: ahar  
> Created at: 2017-11-06 14:15:26 UTC  
> Updated at: 2017-11-08 21:15:21 UTC  
> Closed at: 2017-11-08 21:15:21 UTC  
> Url: https://github.com/boostorg/interprocess/issues/42  

Hi,  
I am trying to use `boost` `shared_memory_object` to write data to a block of memory from one .cpp file (with main section). Then, from another .cpp file (with main section), I want to read it. I tried example 33.6 in  [this](https://theboostcpplibraries.com/boost.interprocess-managed-shared-memory) . I am using cmake to build it in ubuntu. Boost version 1.54 is there in `/usr/include` of my system. Following is the `CMakeLists.txt `  
  
```  
set(CMAKE_BUILD_TYPE Debug)  
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")  
project (test1)  
cmake_minimum_required (VERSION 2.8)  
FIND_PACKAGE( Boost REQUIRED COMPONENTS system )  
INCLUDE_DIRECTORIES( ${Boost_INCLUDE_DIR} )  
add_executable(Power2 main.cpp)  
TARGET_LINK_LIBRARIES( Power2 LINK_PUBLIC ${Boost_LIBRARIES} )  
```  
  
But while building either using Qt or cmake followed by make, I get following error.                                                        `In functionboost::interprocess::shared_memory_object::priv_open_or_create(boost::interprocess:: ipcdetail:: create_enum_t, char const*, boost::interprocess::mode_t, boost::interprocess::permissions const&)':`  
Then, `undefined reference to shm_open` and `undefined reference to shm_unlink`   
  
I am unable to understand the source of the problem. Please look into it and help me out of the problem.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2017-11-08 21:15:20 UTC  
> Url: https://github.com/boostorg/interprocess/issues/42#issuecomment-342962069  

This is not problem in the library. See the documentation:  
  
http://www.boost.org/doc/libs/1_65_1/doc/html/interprocess.html#interprocess.intro.introduction_building_interprocess  
  
You need to add -lrt to your command line in some systems. Try:  
  
target_link_libraries(module -lrt)  
  
or similar, to add in you library list.
