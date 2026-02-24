# #206 - boost::interprocess::named_condition compile error on Visual Studio 2022 [Closed]

> Username: AlexisLeConte  
> Created at: 2023-11-06 00:13:55 UTC  
> Updated at: 2024-07-05 08:13:16 UTC  
> Closed at: 2024-07-05 08:13:16 UTC  
> Url: https://github.com/boostorg/interprocess/issues/206  

I am not able to compile this minimal example program on Visual Studio 2022 (v17.7.6) with Boost 1.83.0:  
```cpp  
#include <boost/interprocess/sync/named_mutex.hpp>  
#include <boost/interprocess/sync/named_condition.hpp>  
#include <boost/interprocess/sync/scoped_lock.hpp>  
#include <boost/chrono/chrono.hpp>  
  
int main()  
{  
    boost::interprocess::named_mutex mtx(boost::interprocess::open_only, "mtx");  
    boost::interprocess::named_condition cv(boost::interprocess::open_only, "cv");  
    boost::interprocess::scoped_lock<boost::interprocess::named_mutex> lock(mtx);  
    if (cv.wait_for(mtx, boost::chrono::milliseconds(100)) == boost::interprocess::cv_status::timeout) // build error  
    {  
        return 1;  
    }  
    return 0;  
}  
```  
Build output:  
```  
>------ Build All started: Project: interprocess_named_mutex_minimal, Configuration: windows-x64-windows-x64 ------  
  MSBuild version 17.7.2+d6990bcfa for .NET Framework  
    
    main.cpp  
D:\Code\Libraries\boost_1_83_0\boost\interprocess\sync\detail\locks.hpp(35,27): error C3083: 'mutex_type': the symbol to the left of a '::' must be a type   
  D:\Code\Libraries\boost_1_83_0\boost/interprocess/sync/named_condition.hpp(264,38): message : see reference to class template instantiation 'boost::interprocess::ipcdetail::internal_mutex_lock<L>' being compiled [D:\Code\interprocess_named_mutex_minimal\build_x64\minimal_example.vcxproj]  
            with  
            [  
                L=boost::interprocess::named_mutex  
            ]  
  D:\Code\Libraries\boost_1_83_0\boost/interprocess/sync/named_condition.hpp(156,18): message : see reference to function template instantiation 'bool boost::interprocess::named_condition::timed_wait<L,TimePoint>(L &,const TimePoint &)' being compiled [D:\Code\interprocess_named_mutex_minimal\build_x64\minimal_example.vcxproj]  
            with  
            [  
                L=boost::interprocess::named_mutex,  
                TimePoint=boost::interprocess::ipcdetail::ustime  
            ]  
  D:\Code\Libraries\boost_1_83_0\boost/interprocess/sync/named_condition.hpp(168,18): message : see reference to function template instantiation 'boost::interprocess::cv_status boost::interprocess::named_condition::wait_until<L,boost::interprocess::ipcdetail::ustime>(L &,const TimePoint &)' being compiled [D:\Code\interprocess_named_mutex_minimal\build_x64\minimal_example.vcxproj]  
            with  
            [  
                L=boost::interprocess::named_mutex,  
                TimePoint=boost::interprocess::ipcdetail::ustime  
            ]  
  D:\Code\interprocess_named_mutex_minimal\main.cpp(11,11): message : see reference to function template instantiation 'boost::interprocess::cv_status boost::interprocess::named_condition::wait_for<boost::interprocess::named_mutex,boost::chrono::milliseconds>(L &,const Duration &)' being compiled [D:\Code\interprocess_named_mutex_minimal\build_x64\minimal_example.vcxproj]  
            with  
            [  
                L=boost::interprocess::named_mutex,  
                Duration=boost::chrono::milliseconds  
            ]  
  
Build All failed.  
```  
CMakeLists.txt:  
```cmake  
cmake_minimum_required (VERSION 3.22)  
project (minimal_example)  
  
set (CMAKE_CXX_STANDARD 20)  
set (CMAKE_CXX_STANDARD_REQUIRED TRUE)  
  
set (BOOST_ROOT "" CACHE PATH "Preferred Boost root directory")  
set (BOOST_INCLUDEDIR "" CACHE PATH "Preferred Boost include directory")  
set (BOOST_LIBRARYDIR "" CACHE PATH "Preferred Boost library directory")  
find_package (Boost 1.83.0 REQUIRED COMPONENTS system chrono date_time)  
  
add_executable (minimal_example main.cpp)  
target_link_libraries (minimal_example PRIVATE Boost::headers Boost::system Boost::chrono Boost::date_time)  
  
# Visual Studio seems to have trouble finding the Boost library directory  
target_link_directories (minimal_example PRIVATE ${BOOST_LIBRARYDIR})  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-07-05 08:13:16 UTC  
> Url: https://github.com/boostorg/interprocess/issues/206#issuecomment-2210411739  

The problem is that cv.wait_for expects a Lock as the first parameter, and you are passing a mutex, not a Lock. If you change:  
  
` if (cv.wait_for(mtx, `  
  
with  
  
` if (cv.wait_for(lock, ....`  
  
it will work.
