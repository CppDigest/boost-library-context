# #173 - -Werror will break oneAPI build [Closed]

> Username: aminiussi  
> Created at: 2023-08-02 10:48:54 UTC  
> Updated at: 2023-08-16 19:34:48 UTC  
> Closed at: 2023-08-16 19:34:48 UTC  
> Url: https://github.com/boostorg/mysql/issues/173  

OneAPI build easily end up with unsupported options inherited from legacy Intel. These are warning with oneAPI, maklng them errors will break the build.  
  
The following patch seems to fix the issue:  
```  
12:44:33 [alainm@castor mysql]# git diff -c  
diff --git a/cmake/test_utils.cmake b/cmake/test_utils.cmake  
index 2bf0cc7..c77cbd2 100644  
--- a/cmake/test_utils.cmake  
+++ b/cmake/test_utils.cmake  
@@ -38,6 +38,8 @@ function(boost_mysql_common_target_settings TARGET_NAME)  
             _SILENCE_CXX17_ADAPTOR_TYPEDEFS_DEPRECATION_WARNING # Warnings in C++17 for Asio  
         )  
         target_compile_options(${TARGET_NAME} PUBLIC /bigobj) # Prevent failures on Windows  
+    elseif (CMAKE_CXX_COMPILER_ID STREQUAL "IntelLLVM")  
+        target_compile_options(${TARGET_NAME} PUBLIC -Wall -Wextra -pedantic)  
     else()  
         target_compile_options(${TARGET_NAME} PUBLIC -Wall -Wextra -pedantic -Werror)  
     endif()  
12:44:37 [alainm@castor mysql]#   
```

---

## Comment 1

> Username: anarthal  
> Created at: 2023-08-03 14:34:13 UTC  
> Url: https://github.com/boostorg/mysql/issues/173#issuecomment-1664103587  

I can add this, but I'd like to understand more first - I was expecting this cmake code to not be run by user projects, how are you consuming Boost?  
  
I'd also like to add a CI configuration to verify that the build works with the Intel compiler. I don't know anything about oneAPI - could you please tell me which version of the Intel compiler and with which flags are you building the library? Thanks.

---

## Comment 2

> Username: aminiussi  
> Created at: 2023-08-04 12:55:09 UTC  
> Url: https://github.com/boostorg/mysql/issues/173#issuecomment-1665563834  

As I deal with Boost.MPI maintenance/testing, I find it more convenient to use CMake than bjam. So I tried to build the would boost as a no brainer to get the dependencies.  
  
And since we're moving to the new Intel oneAPI compiler (icpx, which is clang based, as opposed to the legacy intel compiler icpc, which is an in house code generator with EDG front end).  
  
Now, the current boost build (both cmake and bjam) uses icpc options when intel compiler is detected, that these option are not necessarily supported by icpx. Note that current Intel distribution provide both icpc and icpx.  
  
As for Boost buil with cmake, I configure with   
```  
export CXX=icpx  
export CC=icx  
export I_MPI_CC=icx      # for Intel MPI  
export I_MPI_CXX=icpx #     ''  
./bootstrap --with-toolset=intel-linux  
# then I edit ./project-config.jam to add  
# using mpi : mpicc ;  
mkdir oneAPI  
cd oneAPI  
cmake -DCMAKE_BUILD_TYPE=Release -DBOOST_ENABLE_MPI=On -DBUILD_TESTING=On ../  
make -j128  
```

---

## Comment 3

> Username: anarthal  
> Created at: 2023-08-09 11:15:04 UTC  
> Url: https://github.com/boostorg/mysql/issues/173#issuecomment-1671133038  

Sorry for the late answer. I've reproduced the warnings, which point to issues with Intel's compiler handling of floating-point numbers. For example:  
  
```  
/home/ruben/workspace/boost-root/libs/mysql/include/boost/mysql/impl/internal/protocol/deserialize_binary_field.ipp:104:9: error: explicit comparison with NaN in fast floating point mode [-Werror,-Wtautological-constant-compare]  
    if (std::isnan(v) || std::isinf(v))  
```  
  
Can you please confirm that these are the warnings you're seeing?  
  
Just disabling them looks like a bad idea, since these checks are there for a reason - these values represent database values that can be controlled by a user, which has security implications.  
  
If you're using cmake to test Boost.MPI in test mode, I strongly recommend just building MPI by using  
  
```  
cmake -DCMAKE_BUILD_TYPE=Release -DBOOST_INCLUDE_LIBRARIES=mpi -DBOOST_ENABLE_MPI=ON -DBUILD_TESTING=ON ../  
```  
  
Otherwise, you're building and running all the tests for all Boost libraries, which must be incredibly expensive.  
  
I'll try to set up a CI for the Intel compiler and fix these warnings for 1.84, in case anyone needs to build MySQL under oneAPI - I've raised #174 for it.  
  
Please let me know if `-DBOOST_INCLUDE_LIBRARIES=mpi` works for you, and I'll close this issue.  
  
Regards,  
Ruben.

---

## Comment 4

> Username: anarthal  
> Created at: 2023-08-16 19:34:48 UTC  
> Url: https://github.com/boostorg/mysql/issues/173#issuecomment-1681167169  

I'm assuming this worked.
