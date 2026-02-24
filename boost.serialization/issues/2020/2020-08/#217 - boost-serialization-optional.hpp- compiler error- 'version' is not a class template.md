# #217 - boost/serialization/optional.hpp: compiler error: 'version' is not a class template [Open]

> Username: jngrad  
> Created at: 2020-08-17 19:08:21 UTC  
> Updated at: 2020-12-02 15:36:30 UTC  
> Url: https://github.com/boostorg/serialization/issues/217  

## Data sheet  
  
* product: boost compiled from sources  
* component: `boost::optional`  
* version: 1.74  
* platform: Fedora 32 in a docker container ([dockerfile](https://github.com/boostorg/serialization/files/5086266/Dockerfile-32-openmpi-boost174.txt)) running on an x86_64 machine with Ubuntu 18.04  
  
## Description of the problem  
  
Including header file `boost/serialization/optional.hpp` in an otherwise empty source file generates a compiler error.  
  
Initially reported in espressomd/espresso#3864 by @junghans for the Gentoo Linux platform (upstream ticket: https://bugs.gentoo.org/737534).  
  
## Steps to reproduce  
  
* build a Fedora 32 docker image ([dockerfile](https://github.com/boostorg/serialization/files/5086266/Dockerfile-32-openmpi-boost174.txt), run with `docker run --user espresso -it fedora:bugreport bash`)  
* compile boost 1.74 from sources with OpenMPI 4.0.4 without UCX  
* compile a simple C++ source file including `boost/serialization/optional.hpp` with gcc 10.2.1  
  
**Bug 1**  
  
MWE: `a.cpp`  
  
```c++  
#include <boost/serialization/optional.hpp>  
int main () {  
  return 0;  
}  
```  
  
Compile command:  
```sh  
c++ -isystem /opt/boost/include -std=c++14 -c a.cpp  
```  
  
Actual results:  
```  
In file included from a.cpp:1:  
/opt/boost/include/boost/serialization/optional.hpp:98:8: error: ‘version’ is not a class template  
   98 | struct version<boost::optional<T> > {  
      |        ^~~~~~~  
```  
  
Expected results: no compiler error.  
  
**Bug 2**  
  
Interaction with `boost::mpi`.  
  
MWE: `b.cpp`  
```c++  
#include <boost/serialization/optional.hpp>  
#include <boost/mpi/communicator.hpp>  
int main () {  
  return 0;  
}  
```  
  
Compile command:  
```sh  
c++ -isystem /opt/boost/include -std=c++14 -c b.cpp  
```  
  
Actual results: verbose compiler error ([compiler.log](https://github.com/boostorg/serialization/files/5086269/compiler.log))  
  
Expected results: either no compiler error, or compiler error `/usr/include/boost/mpi/config.hpp:20:10: fatal error: mpi.h: No such file or directory` if the compiler has no default include path for `mpi.h`.  
  
## Additional info  
  
Reverting c7761f233 and 6af3783a2c for `include/boost/serialization/optional.hpp` suppresses the compiler error and makes the espresso 4.1.3 testsuite pass. This is just a lead, I'm not very familiar with the boost serialization code. Here's my diff:  
  
```diff  
--- /opt/boost/include/boost/serialization/optional.hpp  
+++ /opt/boost/include/boost/serialization/optional.hpp  
@@ -15,6 +15,7 @@  
 #endif  
   
 #include <boost/config.hpp>  
+#include <boost/archive/detail/basic_iarchive.hpp>  
   
 #include <boost/optional.hpp>  
 #include <boost/move/utility_core.hpp>  
@@ -23,7 +24,7 @@  
 #include <boost/serialization/split_free.hpp>  
 #include <boost/serialization/level.hpp>  
 #include <boost/serialization/nvp.hpp>  
-#include <boost/serialization/library_version_type.hpp>  
+#include <boost/serialization/version.hpp>  
 #include <boost/type_traits/is_pointer.hpp>  
 #include <boost/serialization/detail/stack_constructor.hpp>  
 #include <boost/serialization/detail/is_default_constructible.hpp>  
@@ -73,10 +74,10 @@ void load(  
   
     if(0 == version){  
         boost::serialization::item_version_type item_version(0);  
-        boost::serialization::library_version_type library_version(  
+        boost::archive::library_version_type library_version(  
             ar.get_library_version()  
         );  
-        if(boost::serialization::library_version_type(3) < library_version){  
+        if(boost::archive::library_version_type(3) < library_version){  
             ar >> BOOST_SERIALIZATION_NVP(item_version);  
         }  
     }  
```

---

## Comment 1

> Username: thebrandre  
> Created at: 2020-10-10 19:33:05 UTC  
> Url: https://github.com/boostorg/serialization/issues/217#issuecomment-706600403  

I guess, this is the same problem as in issue #210. It should fixed on 'develop' in [this commit](https://github.com/boostorg/serialization/commit/8acf32935bd76d4013902e4841d33b7ffd62c5e5).  
  
But it hasn't been merged into master so far ... can we have that fix for the upcoming 1.75 release?

---

## Comment 2

> Username: junghans  
> Created at: 2020-10-20 19:23:53 UTC  
> Url: https://github.com/boostorg/serialization/issues/217#issuecomment-713086095  

> But it hasn't been merged into master so far ... can we have that fix for the upcoming 1.75 release?  
  
Yes, please!

---

## Comment 3

> Username: ericriff  
> Created at: 2020-12-02 15:01:14 UTC  
> Updated at: 2020-12-02 15:36:30 UTC  
> Url: https://github.com/boostorg/serialization/issues/217#issuecomment-737284894  

This also affects GTSAM 4.0.3, it won't build.  
It works fine with boost 1.73.0.  
  
**EDIT**   
Adding the `<boost/serialization/version.hpp>` header to `include/boost/serialization/optional.hpp` fixes the issue.  
https://github.com/boostorg/serialization/commit/8acf32935bd76d4013902e4841d33b7ffd62c5e5
