# #2402 - LInux getting Could NOT find Boost (missing: Boost_INCLUDE_DIR coroutine filesystem   system thread) whentring to compile examples [Closed]

> Username: meiry  
> Created at: 2022-04-03 20:03:37 UTC  
> Updated at: 2024-09-05 14:12:22 UTC  
> Closed at: 2022-07-21 00:35:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2402  

Trying to compile in linux :  
```  
g++ (Ubuntu 10.3.0-1ubuntu1~18.04~1) 10.3.0  
NAME="Ubuntu"  
VERSION="18.04.6 LTS (Bionic Beaver)"  
  
vagrant@vagrant:~/cpp/boost/libs/beast/build$ echo $BOOST_DIR/  
/home/vagrant/cpp/boost/  
vagrant@vagrant:~/cpp/boost/libs/beast/build$ echo $BOOST_INCLUDEDIR/  
/home/vagrant/cpp/boost/  
vagrant@vagrant:~/cpp/boost/libs/beast/build$ echo $BOOST_ROOT/  
/home/vagrant/cpp/boost/  
  
pwd  
/home/vagrant/cpp/boost  
vagrant@vagrant:~/cpp/boost$ ls -l  
total 120  
-rw-rw-r--   1 vagrant vagrant  1219 Apr  3 05:09 appveyor.yml  
-rw-rw-r--   1 vagrant vagrant   850 Apr  3 05:09 boost-build.jam  
-rw-rw-r--   1 vagrant vagrant 20013 Apr  3 05:09 boostcpp.jam  
-rw-rw-r--   1 vagrant vagrant   989 Apr  3 05:09 boost.css  
-rw-rw-r--   1 vagrant vagrant  6308 Apr  3 05:09 boost.png  
-rw-rw-r--   1 vagrant vagrant  2441 Apr  3 05:09 bootstrap.bat  
-rwxrwxr-x   1 vagrant vagrant 10811 Apr  3 05:09 bootstrap.sh  
-rw-rw-r--   1 vagrant vagrant   821 Apr  3 05:09 CMakeLists.txt  
drwxrwxr-x   7 vagrant vagrant  4096 Apr  3 05:09 doc  
-rw-rw-r--   1 vagrant vagrant   769 Apr  3 05:09 index.htm  
-rw-rw-r--   1 vagrant vagrant  6037 Apr  3 05:09 index.html  
-rw-rw-r--   1 vagrant vagrant   291 Apr  3 05:09 INSTALL  
-rw-rw-r--   1 vagrant vagrant 11947 Apr  3 05:09 Jamroot  
drwxrwxr-x 145 vagrant vagrant  4096 Apr  3 05:09 libs  
-rw-rw-r--   1 vagrant vagrant  1338 Apr  3 05:09 LICENSE_1_0.txt  
drwxrwxr-x   4 vagrant vagrant  4096 Apr  3 05:17 more  
-rw-rw-r--   1 vagrant vagrant   542 Apr  3 05:09 README.md  
-rw-rw-r--   1 vagrant vagrant  2608 Apr  3 05:09 rst.css  
drwxrwxr-x   2 vagrant vagrant  4096 Apr  3 05:09 status  
drwxrwxr-x  14 vagrant vagrant  4096 Apr  3 05:09 tools  
  
~/cpp/boost/libs$ ls  
accumulators     chrono           coroutine       foreach         histogram     lambda2          mp11              pfr                       proto          smart_ptr        tokenizer     vmd  
algorithm        circular_buffer  coroutine2      format          hof           leaf             mpi               phoenix                   ptr_container  sort             tti           wave  
align            compatibility    crc             function        icl           lexical_cast     mpl               platform_maintainers.txt  python         spirit           tuple         winapi  
any              compute          date_time       functional      index.html    libraries.htm    msm               poly_collection           qvm            stacktrace       type_erasure  xpressive  
array            concept_check    describe        function_types  integer       locale           multi_array       polygon                   random         statechart       type_index    yap  
asio             config           detail          fusion          interprocess  local_function   multi_index       pool                      range          static_assert    typeof  
assert           container        dll             geometry        intrusive     lockfree         multiprecision    predef                    ratio          static_string    type_traits  
assign           container_hash   dynamic_bitset  gil             io            log              nowide            preprocessor              rational       stl_interfaces   units  
atomic           context          endian          graph           iostreams     logic            numeric           process                   regex          system           unordered  
beast            contract         exception       graph_parallel  iterator      maintainers.txt  optional          program_options           safe_numerics  test             utility  
bimap            conversion       fiber           hana            Jamfile.v2    math             outcome           property_map              scope_exit     thread           uuid  
bind             convert          filesystem      headers         json          metaparse        parameter         property_map_parallel     serialization  throw_exception  variant  
callable_traits  core             flyweight       heap            lambda        move             parameter_python  property_tree             signals2       timer            variant2  
  
```  
Looks like all boost configuration in placee   
but when i try to compile the example im getting :  
```  
 pwd  
/home/vagrant/cpp/boost/libs/beast/build  
vagrant@vagrant:~/cpp/boost/libs/beast/build$ cmake ..  
CMake Error at /usr/local/share/cmake-3.22/Modules/FindPackageHandleStandardArgs.cmake:230 (message):  
  Could NOT find Boost (missing: Boost_INCLUDE_DIR coroutine filesystem  
  system thread)  
Call Stack (most recent call first):  
  /usr/local/share/cmake-3.22/Modules/FindPackageHandleStandardArgs.cmake:594 (_FPHSA_FAILURE_MESSAGE)  
  /usr/local/share/cmake-3.22/Modules/FindBoost.cmake:2375 (find_package_handle_standard_args)  
  CMakeLists.txt:191 (find_package)  
  
  
-- Configuring incomplete, errors occurred!  
See also "/home/vagrant/cpp/boost/libs/beast/build/CMakeFiles/CMakeOutput.log".  
See also "/home/vagrant/cpp/boost/libs/beast/build/CMakeFiles/CMakeError.log".  
vagrant@vagrant:~/cpp/boost/libs/beast/build$  
```

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-04-04 20:08:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2402#issuecomment-1087963884  

The short answer is that you need a separate boost installation on your platform. Use something like `sudo apt-get install libboost-all-dev` or the usual instructions in the [getting started guide](https://www.boost.org/doc/libs/1_78_0/more/getting_started/unix-variants.html).  
  
Long answer: I just tried  
  
```bash  
mkdir build  
cd build  
cmake .. # or cmake .. -D Beast_BUILD_EXAMPLES=ON -D Beast_BUILD_TESTS=OFF to control what to build  
cmake --build .  
```  
  
and both the configuration and building went fine. This is the result of the configuration step:  
  
```  
-- The C compiler identification is GNU 11.2.0  
-- The CXX compiler identification is GNU 11.2.0  
-- Detecting C compiler ABI info  
-- Detecting C compiler ABI info - done  
-- Check for working C compiler: /usr/bin/cc - skipped  
-- Detecting C compile features  
-- Detecting C compile features - done  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: /usr/bin/c++ - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Looking for pthread.h  
-- Looking for pthread.h - found  
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD  
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Success  
-- Found Threads: TRUE    
-- Found OpenSSL: /usr/lib/x86_64-linux-gnu/libcrypto.so (found version "1.1.1l")    
-- Found Boost: /usr/local/lib/cmake/Boost-1.79.0/BoostConfig.cmake (found version "1.79.0") found components: coroutine filesystem system thread   
-- Configuring done  
-- Generating done  
-- Build files have been written to: /home/alandefreitas/Documents/Code/C++/boost/libs/beast/build  
```  
  
Notice how it found boost in `Found Boost: /usr/local/lib/cmake/Boost-1.79.0/BoostConfig.cmake`. What happens here is that the CMakeLists.txt script takes a different path [depending on whether](thub.com/boostorg/beast/blob/17141a331ad4943e76933e4db51ef48a170aaf84/CMakeLists.txt#L1) the build is considered 1) [a subproject of Boost](https://github.com/boostorg/beast/blob/17141a331ad4943e76933e4db51ef48a170aaf84/CMakeLists.txt#L10) called `boost_beast` or 2) [an independent library](https://github.com/boostorg/beast/blob/17141a331ad4943e76933e4db51ef48a170aaf84/CMakeLists.txt#L85) that depends on Boost called `Beast`. We are recurring to option 2 here, that also includes the tests and examples.  
  
This means we are configuring [a Beast project](https://github.com/boostorg/beast/blob/17141a331ad4943e76933e4db51ef48a170aaf84/CMakeLists.txt#L85) that will look for [OpenSSL](https://github.com/boostorg/beast/blob/17141a331ad4943e76933e4db51ef48a170aaf84/CMakeLists.txt#L162) and [Boost](https://github.com/boostorg/beast/blob/17141a331ad4943e76933e4db51ef48a170aaf84/CMakeLists.txt#L191) on your platform instead of using the Boost library source files in the parent subdirectory. For instance, you can see the configuration step found boost in `/usr/local/lib/cmake/Boost-1.79.0/BoostConfig.cmake`. The files in the parent directory are not important in this case.

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-06-14 19:37:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2402#issuecomment-1155636957  

@meiry  
  
Does   
  
https://github.com/boostorg/beast/issues/2402#issuecomment-1087963884  
  
resolve the issue?

---

## Comment 3

> Username: Star-Dad  
> Created at: 2022-07-20 18:51:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2402#issuecomment-1190636076  

It (the short answer) did for me using Ubuntu 22 LTS Mate.  But now there are other missing entries I have to find :)

---

## Comment 4

> Username: alandefreitas  
> Created at: 2022-07-21 00:35:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2402#issuecomment-1190905923  

Great :)

---

## Comment 5

> Username: ucycg  
> Created at: 2024-09-05 14:12:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2402#issuecomment-2331793063  

Perfect Solution here for me! I previously installed cmake with apt package and had this error and no idea why or how to fix it. this quick cmd line (short answer) did the trick TY! you save me quite some time!
