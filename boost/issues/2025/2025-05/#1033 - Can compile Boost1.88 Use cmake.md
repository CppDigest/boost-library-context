# #1033 - Can compile Boost1.88 Use cmake [Open]

> Username: adjfangjiawei  
> Created at: 2025-05-04 03:06:34 UTC  
> Updated at: 2025-09-04 03:30:37 UTC  
> Url: https://github.com/boostorg/boost/issues/1033  

cmake . -B Build -G Ninja -DCMAKE_BUILD_TYPE=Debug -DCMAKE_EXPORT_COMPILE_COMMANDS=ON -DBUILD_TESTING=ON  
system ubuntu22.04  
compiler g++-11  
  
root@adj-Rack-Server:/media/root/00ddc85d-7a69-49c2-b1ae-30a59772eeb6/library/boost-1.88.0# cmake --build ./Build/ -j 128  
[0/2] Re-checking globbed directories...  
[1/7] Linking CXX executable stage/bin/boost_process_v2_utf8  
FAILED: stage/bin/boost_process_v2_utf8   
: && /usr/bin/c++ -g  libs/process/test/v2/CMakeFiles/boost_process_v2_utf8.dir/utf8.cpp.o -o stage/bin/boost_process_v2_utf8  stage/lib/libboost_process.a  stage/lib/libboost_filesystem.a  stage/lib/libboost_process_v2_test_impl.a  stage/lib/libboost_process.a  stage/lib/libboost_filesystem.a  stage/lib/libboost_atomic.a  stage/lib/libboost_context.a  stage/lib/libboost_date_time.a  stage/lib/libboost_container.a  stage/lib/libboost_unit_test_framework.a && :  
/usr/bin/ld: stage/lib/libboost_unit_test_framework.a(unit_test_main.cpp.o): in function `main':  
/media/root/00ddc85d-7a69-49c2-b1ae-30a59772eeb6/library/boost-1.88.0/libs/test/include/boost/test/impl/unit_test_main.ipp:303: undefined reference to `init_unit_test_suite(int, char**)'  
collect2: error: ld returned 1 exit status  
[2/7] Linking CXX executable stage/bin/boost_process_v2_cstring_ref  
FAILED: stage/bin/boost_process_v2_cstring_ref   
: && /usr/bin/c++ -g  libs/process/test/v2/CMakeFiles/boost_process_v2_cstring_ref.dir/cstring_ref.cpp.o -o stage/bin/boost_process_v2_cstring_ref  stage/lib/libboost_process.a  stage/lib/libboost_filesystem.a  stage/lib/libboost_process_v2_test_impl.a  stage/lib/libboost_process.a  stage/lib/libboost_filesystem.a  stage/lib/libboost_atomic.a  stage/lib/libboost_context.a  stage/lib/libboost_date_time.a  stage/lib/libboost_container.a  stage/lib/libboost_unit_test_framework.a && :  
/usr/bin/ld: stage/lib/libboost_unit_test_framework.a(unit_test_main.cpp.o): in function `main':  
/media/root/00ddc85d-7a69-49c2-b1ae-30a59772eeb6/library/boost-1.88.0/libs/test/include/boost/test/impl/unit_test_main.ipp:303: undefined reference to `init_unit_test_suite(int, char**)'  
collect2: error: ld returned 1 exit status  
[3/7] Linking CXX executable stage/bin/boost_process_v2_environment  
FAILED: stage/bin/boost_process_v2_environment   
: && /usr/bin/c++ -g  libs/process/test/v2/CMakeFiles/boost_process_v2_environment.dir/environment.cpp.o -o stage/bin/boost_process_v2_environment  stage/lib/libboost_process.a  stage/lib/libboost_filesystem.a  stage/lib/libboost_process_v2_test_impl.a  stage/lib/libboost_process.a  stage/lib/libboost_filesystem.a  stage/lib/libboost_atomic.a  stage/lib/libboost_context.a  stage/lib/libboost_date_time.a  stage/lib/libboost_container.a  stage/lib/libboost_unit_test_framework.a && :  
/usr/bin/ld: stage/lib/libboost_unit_test_framework.a(unit_test_main.cpp.o): in function `main':  
/media/root/00ddc85d-7a69-49c2-b1ae-30a59772eeb6/library/boost-1.88.0/libs/test/include/boost/test/impl/unit_test_main.ipp:303: undefined reference to `init_unit_test_suite(int, char**)'  
collect2: error: ld returned 1 exit status  
[4/7] Linking CXX executable stage/bin/boost_process_v2_shell  
FAILED: stage/bin/boost_process_v2_shell   
: && /usr/bin/c++ -g  libs/process/test/v2/CMakeFiles/boost_process_v2_shell.dir/shell.cpp.o -o stage/bin/boost_process_v2_shell  stage/lib/libboost_process.a  stage/lib/libboost_filesystem.a  stage/lib/libboost_process_v2_test_impl.a  stage/lib/libboost_process.a  stage/lib/libboost_filesystem.a  stage/lib/libboost_atomic.a  stage/lib/libboost_context.a  stage/lib/libboost_date_time.a  stage/lib/libboost_container.a  stage/lib/libboost_unit_test_framework.a && :  
/usr/bin/ld: stage/lib/libboost_unit_test_framework.a(unit_test_main.cpp.o): in function `main':  
/media/root/00ddc85d-7a69-49c2-b1ae-30a59772eeb6/library/boost-1.88.0/libs/test/include/boost/test/impl/unit_test_main.ipp:303: undefined reference to `init_unit_test_suite(int, char**)'  
collect2: error: ld returned 1 exit status  
[5/7] Linking CXX executable stage/bin/boost_process_v2_pid  
FAILED: stage/bin/boost_process_v2_pid   
: && /usr/bin/c++ -g  libs/process/test/v2/CMakeFiles/boost_process_v2_pid.dir/pid.cpp.o -o stage/bin/boost_process_v2_pid  stage/lib/libboost_process.a  stage/lib/libboost_filesystem.a  stage/lib/libboost_process_v2_test_impl.a  stage/lib/libboost_process.a  stage/lib/libboost_filesystem.a  stage/lib/libboost_atomic.a  stage/lib/libboost_context.a  stage/lib/libboost_date_time.a  stage/lib/libboost_container.a  stage/lib/libboost_unit_test_framework.a && :  
/usr/bin/ld: stage/lib/libboost_unit_test_framework.a(unit_test_main.cpp.o): in function `main':  
/media/root/00ddc85d-7a69-49c2-b1ae-30a59772eeb6/library/boost-1.88.0/libs/test/include/boost/test/impl/unit_test_main.ipp:303: undefined reference to `init_unit_test_suite(int, char**)'  
collect2: error: ld returned 1 exit status  
[6/7] Linking CXX executable stage/bin/boost_process_v2_ext  
FAILED: stage/bin/boost_process_v2_ext   
: && /usr/bin/c++ -g  libs/process/test/v2/CMakeFiles/boost_process_v2_ext.dir/ext.cpp.o -o stage/bin/boost_process_v2_ext  stage/lib/libboost_process.a  stage/lib/libboost_filesystem.a  stage/lib/libboost_process_v2_test_impl.a  stage/lib/libboost_process.a  stage/lib/libboost_filesystem.a  stage/lib/libboost_atomic.a  stage/lib/libboost_context.a  stage/lib/libboost_date_time.a  stage/lib/libboost_container.a  stage/lib/libboost_unit_test_framework.a && :  
/usr/bin/ld: stage/lib/libboost_unit_test_framework.a(unit_test_main.cpp.o): in function `main':  
/media/root/00ddc85d-7a69-49c2-b1ae-30a59772eeb6/library/boost-1.88.0/libs/test/include/boost/test/impl/unit_test_main.ipp:303: undefined reference to `init_unit_test_suite(int, char**)'  
collect2: error: ld returned 1 exit status  
[7/7] Linking CXX executable stage/bin/boost_process_v2_process  
FAILED: stage/bin/boost_process_v2_process   
: && /usr/bin/c++ -g  libs/process/test/v2/CMakeFiles/boost_process_v2_process.dir/process.cpp.o -o stage/bin/boost_process_v2_process  stage/lib/libboost_process.a  stage/lib/libboost_filesystem.a  stage/lib/libboost_process_v2_test_impl.a  stage/lib/libboost_process.a  stage/lib/libboost_filesystem.a  stage/lib/libboost_atomic.a  stage/lib/libboost_context.a  stage/lib/libboost_date_time.a  stage/lib/libboost_container.a  stage/lib/libboost_unit_test_framework.a && :  
/usr/bin/ld: stage/lib/libboost_unit_test_framework.a(unit_test_main.cpp.o): in function `main':  
/media/root/00ddc85d-7a69-49c2-b1ae-30a59772eeb6/library/boost-1.88.0/libs/test/include/boost/test/impl/unit_test_main.ipp:303: undefined reference to `init_unit_test_suite(int, char**)'  
collect2: error: ld returned 1 exit status

---

## Comment 1

> Username: nigels-com  
> Created at: 2025-08-31 04:34:24 UTC  
> Url: https://github.com/boostorg/boost/issues/1033#issuecomment-3239750044  

Confirming this for boost 1.89.0  
  
```  
FAILED: stage/bin/boost_process_v2_environment   
: && /usr/bin/c++ -g  libs/process/test/v2/CMakeFiles/boost_process_v2_environment.dir/environment.cpp.o -o stage/bin/boost_process_v2_environment  stage/lib/libboost_process.a  stage/lib/libboost_filesystem.a  stage/lib/libboost_process_v2_test_impl.a  stage/lib/libboost_process.a  stage/lib/libboost_filesystem.a  stage/lib/libboost_atomic.a  stage/lib/libboost_date_time.a  stage/lib/libboost_container.a  stage/lib/libboost_context.a  stage/lib/libboost_unit_test_framework.a && :  
/usr/bin/ld: stage/lib/libboost_unit_test_framework.a(unit_test_main.cpp.o): in function `main':  
/home/nigels/dev/boost/libs/test/include/boost/test/impl/unit_test_main.ipp:303:(.text+0x676): undefined reference to `init_unit_test_suite(int, char**)'  
collect2: error: ld returned 1 exit status  
```

---

## Comment 2

> Username: nigels-com  
> Created at: 2025-08-31 04:36:06 UTC  
> Url: https://github.com/boostorg/boost/issues/1033#issuecomment-3239750605  

Succeeds here with: `-DBUILD_TESTING=N`

---

## Comment 3

> Username: nigels-com  
> Created at: 2025-09-04 03:30:37 UTC  
> Url: https://github.com/boostorg/boost/issues/1033#issuecomment-3251671024  

I've filed a PR that fixes this on my setup.
