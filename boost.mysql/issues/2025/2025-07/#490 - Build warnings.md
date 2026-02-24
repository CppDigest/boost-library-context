# #490 - Build warnings [Open]

> Username: Zen0x7  
> Created at: 2025-07-20 04:53:35 UTC  
> Updated at: 2025-07-29 00:43:35 UTC  
> Url: https://github.com/boostorg/mysql/issues/490  

Hi  
  
I'm using 1.87.0 and my CI is showing the following:  
  
```  
In function 'write_pad2',  
    inlined from 'datetime_to_string' at /usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:125:20,  
    inlined from 'append_quoted_datetime' at /usr/local/include/boost/mysql/impl/format_sql.ipp:195:48,  
    inlined from 'append_field_view' at /usr/local/include/boost/mysql/impl/format_sql.ipp:254:61:  
/usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:47:18: warning: writing 1 byte into a region of size 0 [-Wstringop-overflow=]  
   47 |         *begin++ = '0';  
      |                  ^  
/usr/local/include/boost/mysql/impl/format_sql.ipp: In function 'append_field_view':  
/usr/local/include/boost/mysql/impl/format_sql.ipp:193:10: note: at offset 66 into destination object 'buffer' of size 66  
  1[93](https://git.zentrack.cl/zentrack/server/-/jobs/1345#L93) |     char buffer[66];  
      |          ^  
In function 'write_pad2',  
    inlined from 'datetime_to_string' at /usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:129:20,  
    inlined from 'append_quoted_datetime' at /usr/local/include/boost/mysql/impl/format_sql.ipp:1[95](https://git.zentrack.cl/zentrack/server/-/jobs/1345#L95):48,  
    inlined from 'append_field_view' at /usr/local/include/boost/mysql/impl/format_sql.ipp:254:61:  
/usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:47:18: warning: writing 1 byte into a region of size 0 [-Wstringop-overflow=]  
   47 |         *begin++ = '0';  
      |                  ^  
/usr/local/include/boost/mysql/impl/format_sql.ipp: In function 'append_field_view':  
/usr/local/include/boost/mysql/impl/format_sql.ipp:193:10: note: at offset 66 into destination object 'buffer' of size 66  
  193 |     char buffer[66];  
      |          ^  
In function 'write_pad2',  
    inlined from 'time_to_string' at /usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:174:20,  
    inlined from 'append_quoted_time' at /usr/local/include/boost/mysql/impl/format_sql.ipp:213:36,  
    inlined from 'append_field_view' at /usr/local/include/boost/mysql/impl/format_sql.ipp:255:53:  
/usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:47:18: warning: writing 1 byte into a region of size 0 [-Wstringop-overflow=]  
   47 |         *begin++ = '0';  
      |                  ^  
/usr/local/include/boost/mysql/impl/format_sql.ipp: In function 'append_field_view':  
/usr/local/include/boost/mysql/impl/format_sql.ipp:211:10: note: at offset 66 into destination object 'buffer' of size 66  
  211 |     char buffer[66];  
      |          ^  
In function 'write_pad2',  
    inlined from 'time_to_string' at /usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:178:20,  
    inlined from 'append_quoted_time' at /usr/local/include/boost/mysql/impl/format_sql.ipp:213:36,  
    inlined from 'append_field_view' at /usr/local/include/boost/mysql/impl/format_sql.ipp:255:53:  
/usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:47:18: warning: writing 1 byte into a region of size 0 [-Wstringop-overflow=]  
   47 |         *begin++ = '0';  
      |                  ^  
/usr/local/include/boost/mysql/impl/format_sql.ipp: In function 'append_field_view':  
/usr/local/include/boost/mysql/impl/format_sql.ipp:211:10: note: at offset 66 into destination object 'buffer' of size 66  
  211 |     char buffer[66];  
      |          ^  
```  
  
I've several hardened flags:  
  
```  
    set(HARDENED_FLAGS  
            -Wall -Wextra -Wpedantic -Werror  
            -Wshadow -Wnull-dereference -Wdouble-promotion  
            -Wconversion -Wsign-conversion -Wold-style-cast  
            -Wnon-virtual-dtor -Woverloaded-virtual -Wuseless-cast  
            -Wcast-align -Wcast-qual -Wstrict-aliasing=2  
            -Waligned-new=all -Wduplicated-cond -Wduplicated-branches  
            -Wlogical-op -Wmissing-declarations -Wredundant-decls  
            -fsanitize=address,undefined,leak,signed-integer-overflow  
            -fno-omit-frame-pointer  
            -D_FORTIFY_SOURCE=2  
    )  
  
    set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -g --coverage -fsanitize=address,undefined,leak,signed-integer-overflow -fno-omit-frame-pointer")  
```  
  
This is a concern?

---

## Comment 1

> Username: anarthal  
> Created at: 2025-07-20 08:33:58 UTC  
> Url: https://github.com/boostorg/mysql/issues/490#issuecomment-3094353258  

It's almost surely a false positive. I will double check and silence the warning.

---

## Comment 2

> Username: anarthal  
> Created at: 2025-07-27 20:37:40 UTC  
> Url: https://github.com/boostorg/mysql/issues/490#issuecomment-3124713264  

I can confirm that this is a false positive. There is no buffer overrun, and there are sanitizers in-place to check.

---

## Comment 3

> Username: Zen0x7  
> Created at: 2025-07-29 00:43:35 UTC  
> Url: https://github.com/boostorg/mysql/issues/490#issuecomment-3130217727  

**The lib works fine**. I'm running my CI tests on ARM64 over debian:latest based container.  
  
Feel free to close this if you wish.  
  
The CMakeLists.txt is the next:  
  
```  
cmake_minimum_required(VERSION 3.25)  
project(Server)  
  
set(CMAKE_CXX_STANDARD 20)  
set(CMAKE_POSITION_INDEPENDENT_CODE ON)  
  
option(BUILD_TESTS "Build tests cases" ON)  
option(BUILD_HARD "Build hard" ON)  
  
message(STATUS "Using BUILD_TESTS = ${BUILD_TESTS}")  
message(STATUS "Using CMAKE_BUILD_TYPE = ${CMAKE_BUILD_TYPE}")  
  
if(CMAKE_SYSTEM_PROCESSOR MATCHES "aarch64")  
    set(ARCH_LIB_DIR "/usr/lib/aarch64-linux-gnu")  
elseif(CMAKE_SYSTEM_PROCESSOR MATCHES "x86_64")  
    set(ARCH_LIB_DIR "/usr/lib/x86_64-linux-gnu")  
else()  
    message(FATAL_ERROR "Arquitectura no soportada: ${CMAKE_SYSTEM_PROCESSOR}")  
endif()  
  
set(OPENSSL_CRYPTO_LIBRARY "${ARCH_LIB_DIR}/libcrypto.a")  
  
find_package(Boost 1.87.0 REQUIRED COMPONENTS program_options json charconv)  
if(NOT Boost_FOUND)  
    message(FATAL_ERROR "Boost not found")  
endif()  
  
find_package(OpenSSL REQUIRED)  
  
file(GLOB_RECURSE SOURCES CONFIGURE_DEPENDS "${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp")  
  
include(FetchContent)  
  
FetchContent_Declare(  
        fmt  
        GIT_REPOSITORY https://github.com/fmtlib/fmt  
        GIT_TAG        e69e5f977d458f2650bb346dadf2ad30c5320281  
)  
FetchContent_MakeAvailable(fmt)  
  
add_library(BoostLib  
        ${CMAKE_CURRENT_SOURCE_DIR}/deps/asio.cxx  
        ${CMAKE_CURRENT_SOURCE_DIR}/deps/mysql.cxx  
        ${CMAKE_CURRENT_SOURCE_DIR}/deps/redis.cxx  
)  
  
target_include_directories(BoostLib PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)  
target_link_libraries(BoostLib PUBLIC ${Boost_LIBRARIES} OpenSSL::SSL OpenSSL::Crypto bcrypt)  
  
add_library(ServerLib ${SOURCES})  
target_include_directories(ServerLib PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)  
target_link_libraries(ServerLib PRIVATE ${Boost_LIBRARIES} BoostLib fmt::fmt)  
  
add_executable(Server main.cpp)  
target_link_libraries(Server PRIVATE ServerLib fmt::fmt)  
  
if(CMAKE_BUILD_TYPE STREQUAL "Debug")  
  
    set(HARDENED_FLAGS  
            -Wall -Wextra -Wpedantic  
            -Wshadow -Wnull-dereference -Wdouble-promotion  
            -Wconversion -Wsign-conversion -Wold-style-cast  
            -Wnon-virtual-dtor -Woverloaded-virtual -Wuseless-cast  
            -Wcast-align -Wcast-qual -Wstrict-aliasing=2  
            -Waligned-new=all -Wduplicated-cond -Wduplicated-branches  
            -Wlogical-op -Wmissing-declarations -Wredundant-decls  
            -fsanitize=address,undefined,leak,signed-integer-overflow  
            -fno-omit-frame-pointer  
            -D_FORTIFY_SOURCE=2  
    )  
  
    if (BUILD_HARD)  
        message(STATUS "Applying HARDENED DEBUG flags")  
        set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -g --coverage -fsanitize=address,undefined,leak,signed-integer-overflow -fno-omit-frame-pointer")  
  
        target_compile_options(BoostLib PRIVATE ${HARDENED_FLAGS})  
        target_compile_options(ServerLib PRIVATE ${HARDENED_FLAGS})  
        target_compile_options(Server PRIVATE ${HARDENED_FLAGS})  
    else ()  
        set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -g --coverage")  
    endif ()  
  
elseif(CMAKE_BUILD_TYPE STREQUAL "Release")  
  
    set(PERFORMANCE_FLAGS  
            -O3  
            -march=native  
            -flto  
            -fstack-protector-strong  
            -D_FORTIFY_SOURCE=2  
    )  
  
    set(PERFORMANCE_LINKER_FLAGS  
            -Wl,-O3  
            -Wl,-z,relro  
            -Wl,-z,now  
            -Wl,-z,noexecstack  
            -flto  
            -pie  
    )  
  
    if (BUILD_HARD)  
        message(STATUS "Applying PERFORMANCE RELEASE flags")  
        target_compile_options(BoostLib PRIVATE ${PERFORMANCE_FLAGS})  
        target_compile_options(ServerLib PRIVATE ${PERFORMANCE_FLAGS})  
        target_compile_options(Server PRIVATE ${PERFORMANCE_FLAGS})  
        target_link_options(BoostLib PRIVATE ${PERFORMANCE_LINKER_FLAGS})  
        target_link_options(ServerLib PRIVATE ${PERFORMANCE_LINKER_FLAGS})  
        target_link_options(Server PRIVATE ${PERFORMANCE_LINKER_FLAGS})  
    else ()  
        target_compile_options(ServerLib PRIVATE -O3)  
        target_compile_options(Server PRIVATE -O3)  
        target_link_options(ServerLib PRIVATE -O3)  
        target_link_options(Server PRIVATE -O3)  
    endif ()  
endif()  
  
if (BUILD_TESTS)  
    FetchContent_Declare(  
            googletest  
            URL https://github.com/google/googletest/archive/03597a01ee50ed33e9dfd640b249b4be3799d395.zip  
    )  
    set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)  
    FetchContent_MakeAvailable(googletest)  
  
    enable_testing()  
    file(GLOB_RECURSE TESTS CONFIGURE_DEPENDS "${CMAKE_CURRENT_SOURCE_DIR}/tests/*.cc")  
  
    add_executable(tests ${TESTS})  
    target_link_libraries(tests PRIVATE GTest::gtest_main ServerLib fmt::fmt)  
    target_include_directories(tests PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/include)  
  
    if (BUILD_HARD)  
        if(CMAKE_BUILD_TYPE STREQUAL "Debug")  
            target_compile_options(tests PRIVATE ${HARDENED_FLAGS})  
            target_link_options(tests PRIVATE -fsanitize=address,undefined,leak,signed-integer-overflow)  
        elseif(CMAKE_BUILD_TYPE STREQUAL "Release")  
            target_compile_options(tests PRIVATE ${PERFORMANCE_FLAGS})  
            target_link_options(tests PRIVATE ${PERFORMANCE_LINKER_FLAGS})  
        endif()  
    endif ()  
  
    include(GoogleTest)  
    gtest_discover_tests(tests)  
endif()  
```  
  
The CI output is:  
  
```  
[0KRunning with gitlab-runner 17.9.1 (bbf75488)[0;m  
[0K  on runner-a t1_R8CB-_, system ID: s_8008bb3e5d9f[0;m  
section_start:1753480156:prepare_executor  
[0K[0K[36;1mPreparing the "docker" executor[0;m[0;m  
[0KUsing Docker executor with image registry.zentrack.cl/engineering/cpp/base:production ...[0;m  
[0KStarting service arm64v8/mysql:8.4...[0;m  
[0KPulling docker image arm64v8/mysql:8.4 ...[0;m  
[0KUsing docker image sha256:e79d8ebc94dbd6b50abbb4a1e742cdb31edeff79ff083cdf0ed5d8bf86ca18ab for arm64v8/mysql:8.4 with digest arm64v8/mysql@sha256:a0a42d81d4b0deb296fe387acfeb67f0e4917fb7f40d9ee4176bd0c1932dddae ...[0;m  
[0KStarting service bitnami/redis:latest...[0;m  
[0KPulling docker image bitnami/redis:latest ...[0;m  
[0KUsing docker image sha256:fb9be57e79783e6022bc91cffd305026b7c0459f5027af68d0233a603d8b3094 for bitnami/redis:latest with digest bitnami/redis@sha256:5927ff3702df7106bbb7dcc0f8e3d053d979253c3aecce53f07f40db990f823b ...[0;m  
[0KWaiting for services to be up and running (timeout 30 seconds)...[0;m  
[0KAuthenticating with credentials from job payload (GitLab Registry)[0;m  
[0KPulling docker image registry.zentrack.cl/engineering/cpp/base:production ...[0;m  
[0KUsing docker image sha256:3270a9f206567b8e877cc69bee93564e0a427dfd399a292dd25b680718240452 for registry.zentrack.cl/engineering/cpp/base:production with digest registry.zentrack.cl/engineering/cpp/base@sha256:c08010d61ac8029a133dd3b49ffc46e485ab3117bc7843526543687cd33a2fb0 ...[0;m  
section_end:1753480172:prepare_executor  
[0Ksection_start:1753480172:prepare_script  
[0K[0K[36;1mPreparing environment[0;m[0;m  
Running on runner-t1r8cb--project-22-concurrent-0 via runners...  
section_end:1753480172:prepare_script  
[0Ksection_start:1753480172:get_sources  
[0K[0K[36;1mGetting source from Git repository[0;m[0;m  
[32;1mFetching changes with git depth set to 20...[0;m  
Reinitialized existing Git repository in /builds/zentrack/server/.git/  
[32;1mChecking out 26e162aa as detached HEAD (ref is master)...[0;m  
Removing build/CMakeCache.txt  
Removing build/CMakeFiles/  
Removing build/CTestTestfile.cmake  
Removing build/Makefile  
Removing build/Server  
Removing build/Testing/  
Removing build/_deps/  
Removing build/bin/  
Removing build/certificates.ca-bundle  
Removing build/certificates.crt  
Removing build/certificates.key  
Removing build/certificates.params  
Removing build/cmake_install.cmake  
Removing build/lib/  
Removing build/libBoostLib.a  
Removing build/libServerLib.a  
Removing build/rootCA.key  
Removing build/rootCA.pem  
Removing build/tests  
Removing build/tests[1]_include.cmake  
Removing build/tests[1]_tests.cmake  
  
[32;1mSkipping Git submodules setup[0;m  
section_end:1753480173:get_sources  
[0Ksection_start:1753480173:step_script  
[0K[0K[36;1mExecuting "step_script" stage of the job script[0;m[0;m  
[0KUsing docker image sha256:3270a9f206567b8e877cc69bee93564e0a427dfd399a292dd25b680718240452 for registry.zentrack.cl/engineering/cpp/base:production with digest registry.zentrack.cl/engineering/cpp/base@sha256:c08010d61ac8029a133dd3b49ffc46e485ab3117bc7843526543687cd33a2fb0 ...[0;m  
[32;1m$ ping -c 4 mysql[0;m  
PING mysql (172.17.0.2) 56(84) bytes of data.  
64 bytes from arm64v8__mysql (172.17.0.2): icmp_seq=1 ttl=64 time=0.060 ms  
64 bytes from arm64v8__mysql (172.17.0.2): icmp_seq=2 ttl=64 time=0.046 ms  
64 bytes from arm64v8__mysql (172.17.0.2): icmp_seq=3 ttl=64 time=0.047 ms  
64 bytes from arm64v8__mysql (172.17.0.2): icmp_seq=4 ttl=64 time=0.053 ms  
  
--- mysql ping statistics ---  
4 packets transmitted, 4 received, 0% packet loss, time 3099ms  
rtt min/avg/max/mdev = 0.046/0.051/0.060/0.005 ms  
[32;1m$ ping -c 4 redis[0;m  
PING redis (172.17.0.3) 56(84) bytes of data.  
64 bytes from bitnami__redis (172.17.0.3): icmp_seq=1 ttl=64 time=0.050 ms  
64 bytes from bitnami__redis (172.17.0.3): icmp_seq=2 ttl=64 time=0.048 ms  
64 bytes from bitnami__redis (172.17.0.3): icmp_seq=3 ttl=64 time=0.043 ms  
64 bytes from bitnami__redis (172.17.0.3): icmp_seq=4 ttl=64 time=0.039 ms  
  
--- redis ping statistics ---  
4 packets transmitted, 4 received, 0% packet loss, time 3070ms  
rtt min/avg/max/mdev = 0.039/0.045/0.050/0.004 ms  
[32;1m$ nc -zv redis 6379[0;m  
DNS fwd/rev mismatch: redis != bitnami__redis  
redis [172.17.0.3] 6379 (redis) open  
[32;1m$ mysql -u user -p'zentrack_p4ssw0rd' -h mysql -P 3306 -D zentrack -e "exit"[0;m  
[32;1m$ mysql -u user -p'zentrack_p4ssw0rd' -h mysql -P 3306 -D zentrack < dump.sql[0;m  
[32;1m$ cd build[0;m  
[32;1m$ ./generate_certs.sh[0;m  
📦 Generando certificados de prueba para CN=localhost...  
Certificate request self-signature ok  
subject=CN = localhost  
Generating DH parameters, 2048 bit long safe prime  
...........................................................................................................................................................................................................................................................................................................................................................+......................................................................................................................................................................................................................+.....+...............................................................................................................................................................+...................................................................................................................................................................................................................................................................................................+.........+.........................................................+...............................................................................................................................................................................................................................................................................................................................................................................................................+....................................................................................................................................................................................................................+............................................................................................................................................................................................................+.......................................................................................................................................................................................................................................+..............................................................................................................................................+..........+.............................................................................+...........................................................................................................................................................................................................................................................................................................................++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*++*  
✅ Certificados de prueba generados:  
-rw-r--r-- 1 root root 1147 Jul 25 21:49 ./certificates.ca-bundle  
-rw-r--r-- 1 root root 1009 Jul 25 21:49 ./certificates.crt  
-rw------- 1 root root 1704 Jul 25 21:49 ./certificates.key  
-rw-r--r-- 1 root root  424 Jul 25 21:50 ./certificates.params  
[32;1m$ if [ "$ENVIRONMENT" = "develop" ]; then # collapsed multi-line command[0;m  
-- The C compiler identification is GNU 12.2.0  
-- The CXX compiler identification is GNU 12.2.0  
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
-- Using BUILD_TESTS = ON  
-- Using CMAKE_BUILD_TYPE = Release  
-- Found Boost: /usr/local/lib/cmake/Boost-1.87.0/BoostConfig.cmake (found suitable version "1.87.0", minimum required is "1.87.0") found components: program_options json charconv   
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD  
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Success  
-- Found Threads: TRUE    
-- Found OpenSSL: /usr/lib/aarch64-linux-gnu/libcrypto.a (found version "3.0.16")    
-- Version: 10.2.1  
-- Build type: Release  
-- Applying PERFORMANCE RELEASE flags  
-- Found Python: /usr/bin/python3 (found version "3.11.2") found components: Interpreter   
-- Configuring done  
-- Generating done  
-- Build files have been written to: /builds/zentrack/server/build  
[32;1m$ make[0;m  
[  3%] Building CXX object CMakeFiles/BoostLib.dir/deps/asio.cxx.o  
[  7%] Building CXX object CMakeFiles/BoostLib.dir/deps/mysql.cxx.o  
[ 10%] Building CXX object CMakeFiles/BoostLib.dir/deps/redis.cxx.o  
[ 14%] Linking CXX static library libBoostLib.a  
[ 14%] Built target BoostLib  
[ 17%] Building CXX object _deps/fmt-build/CMakeFiles/fmt.dir/src/format.cc.o  
[ 21%] Building CXX object _deps/fmt-build/CMakeFiles/fmt.dir/src/os.cc.o  
[ 25%] Linking CXX static library libfmt.a  
[ 25%] Built target fmt  
[ 28%] Building CXX object CMakeFiles/ServerLib.dir/src/app.cpp.o  
[ 32%] Building CXX object CMakeFiles/ServerLib.dir/src/connection.cpp.o  
[ 35%] Building CXX object CMakeFiles/ServerLib.dir/src/server.cpp.o  
[ 39%] Building CXX object CMakeFiles/ServerLib.dir/src/state.cpp.o  
[ 42%] Building CXX object CMakeFiles/ServerLib.dir/src/task_group.cpp.o  
[ 46%] Building CXX object CMakeFiles/ServerLib.dir/src/validator.cpp.o  
[ 50%] Linking CXX static library libServerLib.a  
[ 50%] Built target ServerLib  
[ 53%] Building CXX object CMakeFiles/Server.dir/main.cpp.o  
[ 57%] Linking CXX executable Server  
lto-wrapper: warning: using serial compilation of 79 LTRANS jobs  
lto-wrapper: note: see the '-flto' option documentation for more information  
In function 'write_pad2',  
    inlined from 'datetime_to_string' at /usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:125:20,  
    inlined from 'append_quoted_datetime' at /usr/local/include/boost/mysql/impl/format_sql.ipp:195:48,  
    inlined from 'append_field_view' at /usr/local/include/boost/mysql/impl/format_sql.ipp:254:61:  
/usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:47:18: warning: writing 1 byte into a region of size 0 [-Wstringop-overflow=]  
   47 |         *begin++ = '0';  
      |                  ^  
/usr/local/include/boost/mysql/impl/format_sql.ipp: In function 'append_field_view':  
/usr/local/include/boost/mysql/impl/format_sql.ipp:193:10: note: at offset 66 into destination object 'buffer' of size 66  
  193 |     char buffer[66];  
      |          ^  
In function 'write_pad2',  
    inlined from 'datetime_to_string' at /usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:129:20,  
    inlined from 'append_quoted_datetime' at /usr/local/include/boost/mysql/impl/format_sql.ipp:195:48,  
    inlined from 'append_field_view' at /usr/local/include/boost/mysql/impl/format_sql.ipp:254:61:  
/usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:47:18: warning: writing 1 byte into a region of size 0 [-Wstringop-overflow=]  
   47 |         *begin++ = '0';  
      |                  ^  
/usr/local/include/boost/mysql/impl/format_sql.ipp: In function 'append_field_view':  
/usr/local/include/boost/mysql/impl/format_sql.ipp:193:10: note: at offset 66 into destination object 'buffer' of size 66  
  193 |     char buffer[66];  
      |          ^  
In function 'write_pad2',  
    inlined from 'time_to_string' at /usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:174:20,  
    inlined from 'append_quoted_time' at /usr/local/include/boost/mysql/impl/format_sql.ipp:213:36,  
    inlined from 'append_field_view' at /usr/local/include/boost/mysql/impl/format_sql.ipp:255:53:  
/usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:47:18: warning: writing 1 byte into a region of size 0 [-Wstringop-overflow=]  
   47 |         *begin++ = '0';  
      |                  ^  
/usr/local/include/boost/mysql/impl/format_sql.ipp: In function 'append_field_view':  
/usr/local/include/boost/mysql/impl/format_sql.ipp:211:10: note: at offset 66 into destination object 'buffer' of size 66  
  211 |     char buffer[66];  
      |          ^  
In function 'write_pad2',  
    inlined from 'time_to_string' at /usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:178:20,  
    inlined from 'append_quoted_time' at /usr/local/include/boost/mysql/impl/format_sql.ipp:213:36,  
    inlined from 'append_field_view' at /usr/local/include/boost/mysql/impl/format_sql.ipp:255:53:  
/usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:47:18: warning: writing 1 byte into a region of size 0 [-Wstringop-overflow=]  
   47 |         *begin++ = '0';  
      |                  ^  
/usr/local/include/boost/mysql/impl/format_sql.ipp: In function 'append_field_view':  
/usr/local/include/boost/mysql/impl/format_sql.ipp:211:10: note: at offset 66 into destination object 'buffer' of size 66  
  211 |     char buffer[66];  
      |          ^  
[ 57%] Built target Server  
[ 60%] Building CXX object _deps/googletest-build/googletest/CMakeFiles/gtest.dir/src/gtest-all.cc.o  
In file included from /usr/include/c++/12/ios:40,  
                 from /usr/include/c++/12/ostream:38,  
                 from /usr/include/c++/12/bits/unique_ptr.h:41,  
                 from /usr/include/c++/12/memory:76,  
                 from /builds/zentrack/server/build/_deps/googletest-src/googletest/include/gtest/gtest.h:54,  
                 from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest-all.cc:38:  
In static member function 'static constexpr std::char_traits<char>::char_type* std::char_traits<char>::copy(char_type*, const char_type*, std::size_t)',  
    inlined from 'static constexpr void std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::_S_copy(_CharT*, const _CharT*, size_type) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]' at /usr/include/c++/12/bits/basic_string.h:423:21,  
    inlined from 'constexpr std::__cxx11::basic_string<_CharT, _Traits, _Allocator>& std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::_M_replace(size_type, size_type, const _CharT*, size_type) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]' at /usr/include/c++/12/bits/basic_string.tcc:532:22,  
    inlined from 'constexpr std::__cxx11::basic_string<_CharT, _Traits, _Alloc>& std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::replace(size_type, size_type, const _CharT*, size_type) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]' at /usr/include/c++/12/bits/basic_string.h:2171:19,  
    inlined from 'constexpr std::__cxx11::basic_string<_CharT, _Traits, _Alloc>& std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::insert(size_type, const _CharT*) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]' at /usr/include/c++/12/bits/basic_string.h:1928:22,  
    inlined from 'constexpr std::__cxx11::basic_string<_CharT, _Traits, _Allocator> std::operator+(const _CharT*, __cxx11::basic_string<_CharT, _Traits, _Allocator>&&) [with _CharT = char; _Traits = char_traits<char>; _Alloc = allocator<char>]' at /usr/include/c++/12/bits/basic_string.h:3541:36,  
    inlined from 'static std::string testing::internal::StreamingListener::UrlEncode(const char*)' at /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest.cc:4900:27:  
/usr/include/c++/12/bits/char_traits.h:431:56: warning: 'void* __builtin_memcpy(void*, const void*, long unsigned int)' accessing 9223372036854775810 or more bytes at offsets [2, 9223372036854775807] and 1 may overlap up to 9223372036854775813 bytes at offset -3 [-Wrestrict]  
  431 |         return static_cast<char_type*>(__builtin_memcpy(__s1, __s2, __n));  
      |                                        ~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~  
[ 64%] Linking CXX static library ../../../lib/libgtest.a  
[ 64%] Built target gtest  
[ 67%] Building CXX object _deps/googletest-build/googletest/CMakeFiles/gtest_main.dir/src/gtest_main.cc.o  
[ 71%] Linking CXX static library ../../../lib/libgtest_main.a  
[ 71%] Built target gtest_main  
[ 75%] Building CXX object CMakeFiles/tests.dir/tests/app_test.cc.o  
[ 78%] Building CXX object CMakeFiles/tests.dir/tests/protocol_test.cc.o  
[ 82%] Building CXX object CMakeFiles/tests.dir/tests/server_test.cc.o  
[ 85%] Linking CXX executable tests  
lto-wrapper: warning: using serial compilation of 86 LTRANS jobs  
lto-wrapper: note: see the '-flto' option documentation for more information  
In function 'write_pad2',  
    inlined from 'datetime_to_string' at /usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:125:20,  
    inlined from 'append_quoted_datetime' at /usr/local/include/boost/mysql/impl/format_sql.ipp:195:48,  
    inlined from 'append_field_view' at /usr/local/include/boost/mysql/impl/format_sql.ipp:254:61:  
/usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:47:18: warning: writing 1 byte into a region of size 0 [-Wstringop-overflow=]  
   47 |         *begin++ = '0';  
      |                  ^  
/usr/local/include/boost/mysql/impl/format_sql.ipp: In function 'append_field_view':  
/usr/local/include/boost/mysql/impl/format_sql.ipp:193:10: note: at offset 66 into destination object 'buffer' of size 66  
  193 |     char buffer[66];  
      |          ^  
In function 'write_pad2',  
    inlined from 'datetime_to_string' at /usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:129:20,  
    inlined from 'append_quoted_datetime' at /usr/local/include/boost/mysql/impl/format_sql.ipp:195:48,  
    inlined from 'append_field_view' at /usr/local/include/boost/mysql/impl/format_sql.ipp:254:61:  
/usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:47:18: warning: writing 1 byte into a region of size 0 [-Wstringop-overflow=]  
   47 |         *begin++ = '0';  
      |                  ^  
/usr/local/include/boost/mysql/impl/format_sql.ipp: In function 'append_field_view':  
/usr/local/include/boost/mysql/impl/format_sql.ipp:193:10: note: at offset 66 into destination object 'buffer' of size 66  
  193 |     char buffer[66];  
      |          ^  
In function 'write_pad2',  
    inlined from 'time_to_string' at /usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:174:20,  
    inlined from 'append_quoted_time' at /usr/local/include/boost/mysql/impl/format_sql.ipp:213:36,  
    inlined from 'append_field_view' at /usr/local/include/boost/mysql/impl/format_sql.ipp:255:53:  
/usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:47:18: warning: writing 1 byte into a region of size 0 [-Wstringop-overflow=]  
   47 |         *begin++ = '0';  
      |                  ^  
/usr/local/include/boost/mysql/impl/format_sql.ipp: In function 'append_field_view':  
/usr/local/include/boost/mysql/impl/format_sql.ipp:211:10: note: at offset 66 into destination object 'buffer' of size 66  
  211 |     char buffer[66];  
      |          ^  
In function 'write_pad2',  
    inlined from 'time_to_string' at /usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:178:20,  
    inlined from 'append_quoted_time' at /usr/local/include/boost/mysql/impl/format_sql.ipp:213:36,  
    inlined from 'append_field_view' at /usr/local/include/boost/mysql/impl/format_sql.ipp:255:53:  
/usr/local/include/boost/mysql/impl/internal/dt_to_string.hpp:47:18: warning: writing 1 byte into a region of size 0 [-Wstringop-overflow=]  
   47 |         *begin++ = '0';  
      |                  ^  
/usr/local/include/boost/mysql/impl/format_sql.ipp: In function 'append_field_view':  
/usr/local/include/boost/mysql/impl/format_sql.ipp:211:10: note: at offset 66 into destination object 'buffer' of size 66  
  211 |     char buffer[66];  
      |          ^  
[ 85%] Built target tests  
[ 89%] Building CXX object _deps/googletest-build/googlemock/CMakeFiles/gmock.dir/src/gmock-all.cc.o  
[ 92%] Linking CXX static library ../../../lib/libgmock.a  
[ 92%] Built target gmock  
[ 96%] Building CXX object _deps/googletest-build/googlemock/CMakeFiles/gmock_main.dir/src/gmock_main.cc.o  
[100%] Linking CXX static library ../../../lib/libgmock_main.a  
[100%] Built target gmock_main  
[32;1m$ ctest -V[0;m  
UpdateCTestConfiguration  from :/builds/zentrack/server/build/DartConfiguration.tcl  
UpdateCTestConfiguration  from :/builds/zentrack/server/build/DartConfiguration.tcl  
Test project /builds/zentrack/server/build  
Constructing a list of tests  
Done constructing a list of tests  
Updating test list for fixtures  
Added 0 tests to meet fixture requirements  
Checking test dependency graph...  
Checking test dependency graph end  
test 1  
      Start  1: server_test.can_accept_connection  
  
1: Test command: /builds/zentrack/server/build/tests "--gtest_filter=server_test.can_accept_connection" "--gtest_also_run_disabled_tests"  
1: Working Directory: /builds/zentrack/server/build  
1: Test timeout computed to be: 10000000  
1: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
1: Note: Google Test filter = server_test.can_accept_connection  
1: [==========] Running 1 test from 1 test suite.  
1: [----------] Global test environment set-up.  
1: [----------] 1 test from server_test  
1: [ RUN      ] server_test.can_accept_connection  
1: [TCP Listener] Waiting for server registration ...  
1: [HTTP Listener] Waiting for server registration ...  
1: [Signals] Ready ...   
1: (Boost.Redis) run-all-op: resolve addresses 172.17.0.3:6379  
1: (Boost.Redis) run-all-op: connected to endpoint 172.17.0.3:6379  
1: (Boost.Redis) writer-op: 117 bytes written.  
1: (Boost.Redis) reader-op: 270 bytes read.  
1: (Boost.Redis) hello-op: Success  
1: subscribe channel 1  
1: [Server Registration] Ready ...   
1: [HTTP Listener] Acceptor was created ...   
1: [TCP Listener] Acceptor was created ...   
1: (Boost.Redis) writer-op: 32 bytes written.  
1: (Boost.Redis) reader-op: 18 bytes read.  
1: [       OK ] server_test.can_accept_connection (2115 ms)  
1: [----------] 1 test from server_test (2115 ms total)  
1:   
1: [----------] Global test environment tear-down  
1: [==========] 1 test from 1 test suite ran. (2115 ms total)  
1: [  PASSED  ] 1 test.  
 1/26 Test  #1: server_test.can_accept_connection ..............................................   Passed    2.12 sec  
test 2  
      Start  2: server_test.can_reject_connections_due_device_not_registered  
  
2: Test command: /builds/zentrack/server/build/tests "--gtest_filter=server_test.can_reject_connections_due_device_not_registered" "--gtest_also_run_disabled_tests"  
2: Working Directory: /builds/zentrack/server/build  
2: Test timeout computed to be: 10000000  
2: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
2: Note: Google Test filter = server_test.can_reject_connections_due_device_not_registered  
2: [==========] Running 1 test from 1 test suite.  
2: [----------] Global test environment set-up.  
2: [----------] 1 test from server_test  
2: [ RUN      ] server_test.can_reject_connections_due_device_not_registered  
2: [TCP Listener] Waiting for server registration ...  
2: [HTTP Listener] Waiting for server registration ...  
2: [Signals] Ready ...   
2: (Boost.Redis) run-all-op: resolve addresses 172.17.0.3:6379  
2: (Boost.Redis) run-all-op: connected to endpoint 172.17.0.3:6379  
2: (Boost.Redis) writer-op: 117 bytes written.  
2: (Boost.Redis) reader-op: 270 bytes read.  
2: (Boost.Redis) hello-op: Success  
2: subscribe channel 1  
2: [Server Registration] Ready ...   
2: [TCP Listener] Acceptor was created ...   
2: [HTTP Listener] Acceptor was created ...   
2: (Boost.Redis) writer-op: 32 bytes written.  
2: (Boost.Redis) reader-op: 18 bytes read.  
2: [       OK ] server_test.can_reject_connections_due_device_not_registered (2105 ms)  
2: [----------] 1 test from server_test (2105 ms total)  
2:   
2: [----------] Global test environment tear-down  
2: [==========] 1 test from 1 test suite ran. (2105 ms total)  
2: [  PASSED  ] 1 test.  
 2/26 Test  #2: server_test.can_reject_connections_due_device_not_registered ...................   Passed    2.11 sec  
test 3  
      Start  3: server_test.can_reject_connections_due_timeout  
  
3: Test command: /builds/zentrack/server/build/tests "--gtest_filter=server_test.can_reject_connections_due_timeout" "--gtest_also_run_disabled_tests"  
3: Working Directory: /builds/zentrack/server/build  
3: Test timeout computed to be: 10000000  
3: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
3: Note: Google Test filter = server_test.can_reject_connections_due_timeout  
3: [==========] Running 1 test from 1 test suite.  
3: [----------] Global test environment set-up.  
3: [----------] 1 test from server_test  
3: [ RUN      ] server_test.can_reject_connections_due_timeout  
3: [TCP Listener] Waiting for server registration ...  
3: [HTTP Listener] Waiting for server registration ...  
3: [Signals] Ready ...   
3: (Boost.Redis) run-all-op: resolve addresses 172.17.0.3:6379  
3: (Boost.Redis) run-all-op: connected to endpoint 172.17.0.3:6379  
3: (Boost.Redis) writer-op: 117 bytes written.  
3: (Boost.Redis) reader-op: 270 bytes read.  
3: (Boost.Redis) hello-op: Success  
3: subscribe channel 1  
3: [Server Registration] Ready ...   
3: [HTTP Listener] Acceptor was created ...   
3: [TCP Listener] Acceptor was created ...   
3: (Boost.Redis) writer-op: 32 bytes written.  
3: (Boost.Redis) reader-op: 18 bytes read.  
3: (Boost.Redis) writer-op: 32 bytes written.  
3: (Boost.Redis) reader-op: 18 bytes read.  
3: (Boost.Redis) writer-op: 32 bytes written.  
3: (Boost.Redis) reader-op: 18 bytes read.  
3: (Boost.Redis) writer-op: 32 bytes written.  
3: (Boost.Redis) reader-op: 18 bytes read.  
3: [       OK ] server_test.can_reject_connections_due_timeout (8104 ms)  
3: [----------] 1 test from server_test (8104 ms total)  
3:   
3: [----------] Global test environment tear-down  
3: [==========] 1 test from 1 test suite ran. (8104 ms total)  
3: [  PASSED  ] 1 test.  
 3/26 Test  #3: server_test.can_reject_connections_due_timeout .................................   Passed    8.11 sec  
test 4  
      Start  4: server_test.can_receive_tcp_records_sample_1  
  
4: Test command: /builds/zentrack/server/build/tests "--gtest_filter=server_test.can_receive_tcp_records_sample_1" "--gtest_also_run_disabled_tests"  
4: Working Directory: /builds/zentrack/server/build  
4: Test timeout computed to be: 10000000  
4: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
4: Note: Google Test filter = server_test.can_receive_tcp_records_sample_1  
4: [==========] Running 1 test from 1 test suite.  
4: [----------] Global test environment set-up.  
4: [----------] 1 test from server_test  
4: [ RUN      ] server_test.can_receive_tcp_records_sample_1  
4: [TCP Listener] Waiting for server registration ...  
4: [HTTP Listener] Waiting for server registration ...  
4: [Signals] Ready ...   
4: (Boost.Redis) run-all-op: resolve addresses 172.17.0.3:6379  
4: (Boost.Redis) run-all-op: connected to endpoint 172.17.0.3:6379  
4: (Boost.Redis) writer-op: 117 bytes written.  
4: (Boost.Redis) reader-op: 270 bytes read.  
4: (Boost.Redis) hello-op: Success  
4: subscribe channel 1  
4: [Server Registration] Ready ...   
4: [TCP Listener] Acceptor was created ... [HTTP Listener] Acceptor was created ...   
4:   
4: (Boost.Redis) writer-op: 32 bytes written.  
4: (Boost.Redis) reader-op: 18 bytes read.  
4: [       OK ] server_test.can_receive_tcp_records_sample_1 (2113 ms)  
4: [----------] 1 test from server_test (2113 ms total)  
4:   
4: [----------] Global test environment tear-down  
4: [==========] 1 test from 1 test suite ran. (2113 ms total)  
4: [  PASSED  ] 1 test.  
 4/26 Test  #4: server_test.can_receive_tcp_records_sample_1 ...................................   Passed    2.12 sec  
test 5  
      Start  5: server_test.can_receive_tcp_records_sample_2  
  
5: Test command: /builds/zentrack/server/build/tests "--gtest_filter=server_test.can_receive_tcp_records_sample_2" "--gtest_also_run_disabled_tests"  
5: Working Directory: /builds/zentrack/server/build  
5: Test timeout computed to be: 10000000  
5: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
5: Note: Google Test filter = server_test.can_receive_tcp_records_sample_2  
5: [==========] Running 1 test from 1 test suite.  
5: [----------] Global test environment set-up.  
5: [----------] 1 test from server_test  
5: [ RUN      ] server_test.can_receive_tcp_records_sample_2  
5: [TCP Listener] Waiting for server registration ...  
5: [HTTP Listener] Waiting for server registration ...  
5: [Signals] Ready ...   
5: (Boost.Redis) run-all-op: resolve addresses 172.17.0.3:6379  
5: (Boost.Redis) run-all-op: connected to endpoint 172.17.0.3:6379  
5: (Boost.Redis) writer-op: 117 bytes written.  
5: (Boost.Redis) reader-op: 271 bytes read.  
5: (Boost.Redis) hello-op: Success  
5: subscribe channel 1  
5: [Server Registration] Ready ...   
5: [HTTP Listener] Acceptor was created ...   
5: [TCP Listener] Acceptor was created ...   
5: (Boost.Redis) writer-op: 32 bytes written.  
5: (Boost.Redis) reader-op: 18 bytes read.  
5: [       OK ] server_test.can_receive_tcp_records_sample_2 (2123 ms)  
5: [----------] 1 test from server_test (2123 ms total)  
5:   
5: [----------] Global test environment tear-down  
5: [==========] 1 test from 1 test suite ran. (2123 ms total)  
5: [  PASSED  ] 1 test.  
 5/26 Test  #5: server_test.can_receive_tcp_records_sample_2 ...................................   Passed    2.13 sec  
test 6  
      Start  6: server_test.can_receive_tcp_records_sample_3  
  
6: Test command: /builds/zentrack/server/build/tests "--gtest_filter=server_test.can_receive_tcp_records_sample_3" "--gtest_also_run_disabled_tests"  
6: Working Directory: /builds/zentrack/server/build  
6: Test timeout computed to be: 10000000  
6: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
6: Note: Google Test filter = server_test.can_receive_tcp_records_sample_3  
6: [==========] Running 1 test from 1 test suite.  
6: [----------] Global test environment set-up.  
6: [----------] 1 test from server_test  
6: [ RUN      ] server_test.can_receive_tcp_records_sample_3  
6: [TCP Listener] Waiting for server registration ...  
6: [HTTP Listener] Waiting for server registration ...  
6: [Signals] Ready ...   
6: (Boost.Redis) run-all-op: resolve addresses 172.17.0.3:6379  
6: (Boost.Redis) run-all-op: connected to endpoint 172.17.0.3:6379  
6: (Boost.Redis) writer-op: 117 bytes written.  
6: (Boost.Redis) reader-op: 271 bytes read.  
6: (Boost.Redis) hello-op: Success  
6: subscribe channel 1  
6: [Server Registration] Ready ...   
6: [HTTP Listener] Acceptor was created ...   
6: [TCP Listener] Acceptor was created ...   
6: (Boost.Redis) writer-op: 32 bytes written.  
6: (Boost.Redis) reader-op: 18 bytes read.  
6: [       OK ] server_test.can_receive_tcp_records_sample_3 (2124 ms)  
6: [----------] 1 test from server_test (2124 ms total)  
6:   
6: [----------] Global test environment tear-down  
6: [==========] 1 test from 1 test suite ran. (2124 ms total)  
6: [  PASSED  ] 1 test.  
 6/26 Test  #6: server_test.can_receive_tcp_records_sample_3 ...................................   Passed    2.13 sec  
test 7  
      Start  7: server_test.can_receive_tcp_commands_sample_1  
  
7: Test command: /builds/zentrack/server/build/tests "--gtest_filter=server_test.can_receive_tcp_commands_sample_1" "--gtest_also_run_disabled_tests"  
7: Working Directory: /builds/zentrack/server/build  
7: Test timeout computed to be: 10000000  
7: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
7: Note: Google Test filter = server_test.can_receive_tcp_commands_sample_1  
7: [==========] Running 1 test from 1 test suite.  
7: [----------] Global test environment set-up.  
7: [----------] 1 test from server_test  
7: [ RUN      ] server_test.can_receive_tcp_commands_sample_1  
7: [TCP Listener] Waiting for server registration ...  
7: [HTTP Listener] Waiting for server registration ...  
7: [Signals] Ready ...   
7: (Boost.Redis) run-all-op: resolve addresses 172.17.0.3:6379  
7: (Boost.Redis) run-all-op: connected to endpoint 172.17.0.3:6379  
7: (Boost.Redis) writer-op: 117 bytes written.  
7: (Boost.Redis) reader-op: 271 bytes read.  
7: (Boost.Redis) hello-op: Success  
7: subscribe channel 1  
7: [Server Registration] Ready ...   
7: [TCP Listener] Acceptor was created ...   
7: [HTTP Listener] Acceptor was created ...   
7: (Boost.Redis) writer-op: 32 bytes written.  
7: (Boost.Redis) reader-op: 18 bytes read.  
7: [       OK ] server_test.can_receive_tcp_commands_sample_1 (2113 ms)  
7: [----------] 1 test from server_test (2113 ms total)  
7:   
7: [----------] Global test environment tear-down  
7: [==========] 1 test from 1 test suite ran. (2113 ms total)  
7: [  PASSED  ] 1 test.  
 7/26 Test  #7: server_test.can_receive_tcp_commands_sample_1 ..................................   Passed    2.12 sec  
test 8  
      Start  8: server_test.can_receive_tcp_commands_sample_2  
  
8: Test command: /builds/zentrack/server/build/tests "--gtest_filter=server_test.can_receive_tcp_commands_sample_2" "--gtest_also_run_disabled_tests"  
8: Working Directory: /builds/zentrack/server/build  
8: Test timeout computed to be: 10000000  
8: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
8: Note: Google Test filter = server_test.can_receive_tcp_commands_sample_2  
8: [==========] Running 1 test from 1 test suite.  
8: [----------] Global test environment set-up.  
8: [----------] 1 test from server_test  
8: [ RUN      ] server_test.can_receive_tcp_commands_sample_2  
8: [TCP Listener] Waiting for server registration ...  
8: [HTTP Listener] Waiting for server registration ...  
8: [Signals] Ready ...   
8: (Boost.Redis) run-all-op: resolve addresses 172.17.0.3:6379  
8: (Boost.Redis) run-all-op: connected to endpoint 172.17.0.3:6379  
8: (Boost.Redis) writer-op: 117 bytes written.  
8: (Boost.Redis) reader-op: 271 bytes read.  
8: (Boost.Redis) hello-op: Success  
8: subscribe channel 1  
8: [Server Registration] Ready ...   
8: [TCP Listener] Acceptor was created ...   
8: [HTTP Listener] Acceptor was created ...   
8: (Boost.Redis) writer-op: 32 bytes written.  
8: (Boost.Redis) reader-op: 18 bytes read.  
8: [       OK ] server_test.can_receive_tcp_commands_sample_2 (2108 ms)  
8: [----------] 1 test from server_test (2108 ms total)  
8:   
8: [----------] Global test environment tear-down  
8: [==========] 1 test from 1 test suite ran. (2108 ms total)  
8: [  PASSED  ] 1 test.  
 8/26 Test  #8: server_test.can_receive_tcp_commands_sample_2 ..................................   Passed    2.12 sec  
test 9  
      Start  9: server_test.can_receive_tcp_commands_sample_3  
  
9: Test command: /builds/zentrack/server/build/tests "--gtest_filter=server_test.can_receive_tcp_commands_sample_3" "--gtest_also_run_disabled_tests"  
9: Working Directory: /builds/zentrack/server/build  
9: Test timeout computed to be: 10000000  
9: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
9: Note: Google Test filter = server_test.can_receive_tcp_commands_sample_3  
9: [==========] Running 1 test from 1 test suite.  
9: [----------] Global test environment set-up.  
9: [----------] 1 test from server_test  
9: [ RUN      ] server_test.can_receive_tcp_commands_sample_3  
9: [TCP Listener] Waiting for server registration ...  
9: [HTTP Listener] Waiting for server registration ...  
9: [Signals] Ready ...   
9: (Boost.Redis) run-all-op: resolve addresses 172.17.0.3:6379  
9: (Boost.Redis) run-all-op: connected to endpoint 172.17.0.3:6379  
9: (Boost.Redis) writer-op: 117 bytes written.  
9: (Boost.Redis) reader-op: 271 bytes read.  
9: (Boost.Redis) hello-op: Success  
9: subscribe channel 1  
9: [Server Registration] Ready ...   
9: [HTTP Listener] Acceptor was created ...   
9: [TCP Listener] Acceptor was created ...   
9: (Boost.Redis) writer-op: 32 bytes written.  
9: (Boost.Redis) reader-op: 18 bytes read.  
9: [       OK ] server_test.can_receive_tcp_commands_sample_3 (2110 ms)  
9: [----------] 1 test from server_test (2110 ms total)  
9:   
9: [----------] Global test environment tear-down  
9: [==========] 1 test from 1 test suite ran. (2110 ms total)  
9: [  PASSED  ] 1 test.  
 9/26 Test  #9: server_test.can_receive_tcp_commands_sample_3 ..................................   Passed    2.12 sec  
test 10  
      Start 10: server_test.can_receive_a_bunch_of_tcp_heartbeats_comma_commands_and_records  
  
10: Test command: /builds/zentrack/server/build/tests "--gtest_filter=server_test.can_receive_a_bunch_of_tcp_heartbeats_comma_commands_and_records" "--gtest_also_run_disabled_tests"  
10: Working Directory: /builds/zentrack/server/build  
10: Test timeout computed to be: 10000000  
10: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
10: Note: Google Test filter = server_test.can_receive_a_bunch_of_tcp_heartbeats_comma_commands_and_records  
10: [==========] Running 1 test from 1 test suite.  
10: [----------] Global test environment set-up.  
10: [----------] 1 test from server_test  
10: [ RUN      ] server_test.can_receive_a_bunch_of_tcp_heartbeats_comma_commands_and_records  
10: [TCP Listener] Waiting for server registration ...  
10: [HTTP Listener] Waiting for server registration ...  
10: [Signals] Ready ...   
10: (Boost.Redis) run-all-op: resolve addresses 172.17.0.3:6379  
10: (Boost.Redis) run-all-op: connected to endpoint 172.17.0.3:6379  
10: (Boost.Redis) writer-op: 117 bytes written.  
10: (Boost.Redis) reader-op: 271 bytes read.  
10: (Boost.Redis) hello-op: Success  
10: subscribe channel 1  
10: [Server Registration] Ready ...   
10: [TCP Listener] Acceptor was created ...   
10: [HTTP Listener] Acceptor was created ...   
10: (Boost.Redis) writer-op: 32 bytes written.  
10: (Boost.Redis) reader-op: 18 bytes read.  
10: -- Writing command  
10: -- Command written  
10: -- Writing record  
10: -- Record written  
10: Reading record response  
10: Record response read  
10: -- Writing heartbeat  
10: -- Heartbeat written  
10: -- Writing two records  
10: -- Two records writen  
10: Reading first record response  
10: First record response read  
10: Reading second record response  
10: Second record response read  
10: -- Writing records with heartbeats  
10: -- Records with heartbeats written  
10: Reading first record response  
10: First record response read  
10: Reading second record response  
10: Second record response read  
10: [       OK ] server_test.can_receive_a_bunch_of_tcp_heartbeats_comma_commands_and_records (2365 ms)  
10: [----------] 1 test from server_test (2366 ms total)  
10:   
10: [----------] Global test environment tear-down  
10: [==========] 1 test from 1 test suite ran. (2366 ms total)  
10: [  PASSED  ] 1 test.  
10/26 Test #10: server_test.can_receive_a_bunch_of_tcp_heartbeats_comma_commands_and_records ...   Passed    2.37 sec  
test 11  
      Start 11: server_test.can_receive_tcp_commands_sample_4  
  
11: Test command: /builds/zentrack/server/build/tests "--gtest_filter=server_test.can_receive_tcp_commands_sample_4" "--gtest_also_run_disabled_tests"  
11: Working Directory: /builds/zentrack/server/build  
11: Test timeout computed to be: 10000000  
11: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
11: Note: Google Test filter = server_test.can_receive_tcp_commands_sample_4  
11: [==========] Running 1 test from 1 test suite.  
11: [----------] Global test environment set-up.  
11: [----------] 1 test from server_test  
11: [ RUN      ] server_test.can_receive_tcp_commands_sample_4  
11: [TCP Listener] Waiting for server registration ...  
11: [HTTP Listener] Waiting for server registration ...  
11: [Signals] Ready ...   
11: (Boost.Redis) run-all-op: resolve addresses 172.17.0.3:6379  
11: (Boost.Redis) run-all-op: connected to endpoint 172.17.0.3:6379  
11: (Boost.Redis) writer-op: 117 bytes written.  
11: (Boost.Redis) reader-op: 271 bytes read.  
11: (Boost.Redis) hello-op: Success  
11: subscribe channel 1  
11: [Server Registration] Ready ...   
11: [TCP Listener] Acceptor was created ...   
11: [HTTP Listener] Acceptor was created ...   
11: (Boost.Redis) writer-op: 32 bytes written.  
11: (Boost.Redis) reader-op: 18 bytes read.  
11: [       OK ] server_test.can_receive_tcp_commands_sample_4 (2108 ms)  
11: [----------] 1 test from server_test (2108 ms total)  
11:   
11: [----------] Global test environment tear-down  
11: [==========] 1 test from 1 test suite ran. (2108 ms total)  
11: [  PASSED  ] 1 test.  
11/26 Test #11: server_test.can_receive_tcp_commands_sample_4 ..................................   Passed    2.12 sec  
test 12  
      Start 12: server_test.can_receive_tcp_heartbeats_and_records_and_respond_ack  
  
12: Test command: /builds/zentrack/server/build/tests "--gtest_filter=server_test.can_receive_tcp_heartbeats_and_records_and_respond_ack" "--gtest_also_run_disabled_tests"  
12: Working Directory: /builds/zentrack/server/build  
12: Test timeout computed to be: 10000000  
12: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
12: Note: Google Test filter = server_test.can_receive_tcp_heartbeats_and_records_and_respond_ack  
12: [==========] Running 1 test from 1 test suite.  
12: [----------] Global test environment set-up.  
12: [----------] 1 test from server_test  
12: [ RUN      ] server_test.can_receive_tcp_heartbeats_and_records_and_respond_ack  
12: [TCP Listener] Waiting for server registration ...  
12: [HTTP Listener] Waiting for server registration ...  
12: [Signals] Ready ...   
12: (Boost.Redis) run-all-op: resolve addresses 172.17.0.3:6379  
12: (Boost.Redis) run-all-op: connected to endpoint 172.17.0.3:6379  
12: (Boost.Redis) writer-op: 117 bytes written.  
12: (Boost.Redis) reader-op: 271 bytes read.  
12: (Boost.Redis) hello-op: Success  
12: subscribe channel 1  
12: [Server Registration] Ready ...   
12: [TCP Listener] Acceptor was created ... [HTTP Listener] Acceptor was created ...   
12:   
12: (Boost.Redis) writer-op: 32 bytes written.  
12: (Boost.Redis) reader-op: 18 bytes read.  
12: [       OK ] server_test.can_receive_tcp_heartbeats_and_records_and_respond_ack (2113 ms)  
12: [----------] 1 test from server_test (2113 ms total)  
12:   
12: [----------] Global test environment tear-down  
12: [==========] 1 test from 1 test suite ran. (2113 ms total)  
12: [  PASSED  ] 1 test.  
12/26 Test #12: server_test.can_receive_tcp_heartbeats_and_records_and_respond_ack .............   Passed    2.12 sec  
test 13  
      Start 13: server_test.can_handle_http_requests  
  
13: Test command: /builds/zentrack/server/build/tests "--gtest_filter=server_test.can_handle_http_requests" "--gtest_also_run_disabled_tests"  
13: Working Directory: /builds/zentrack/server/build  
13: Test timeout computed to be: 10000000  
13: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
13: Note: Google Test filter = server_test.can_handle_http_requests  
13: [==========] Running 1 test from 1 test suite.  
13: [----------] Global test environment set-up.  
13: [----------] 1 test from server_test  
13: [ RUN      ] server_test.can_handle_http_requests  
13: [TCP Listener] Waiting for server registration ...  
13: [HTTP Listener] Waiting for server registration ...  
13: [Signals] Ready ...   
13: (Boost.Redis) run-all-op: resolve addresses 172.17.0.3:6379  
13: (Boost.Redis) run-all-op: connected to endpoint 172.17.0.3:6379  
13: (Boost.Redis) writer-op: 117 bytes written.  
13: (Boost.Redis) reader-op: 271 bytes read.  
13: (Boost.Redis) hello-op: Success  
13: subscribe channel 1  
13: [Server Registration] Ready ...   
13: [TCP Listener] Acceptor was created ...   
13: [HTTP Listener] Acceptor was created ...   
13: (Boost.Redis) writer-op: 32 bytes written.  
13: (Boost.Redis) reader-op: 18 bytes read.  
13: [       OK ] server_test.can_handle_http_requests (2105 ms)  
13: [----------] 1 test from server_test (2105 ms total)  
13:   
13: [----------] Global test environment tear-down  
13: [==========] 1 test from 1 test suite ran. (2105 ms total)  
13: [  PASSED  ] 1 test.  
13/26 Test #13: server_test.can_handle_http_requests ...........................................   Passed    2.11 sec  
test 14  
      Start 14: server_test.can_handle_https_requests  
  
14: Test command: /builds/zentrack/server/build/tests "--gtest_filter=server_test.can_handle_https_requests" "--gtest_also_run_disabled_tests"  
14: Working Directory: /builds/zentrack/server/build  
14: Test timeout computed to be: 10000000  
14: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
14: Note: Google Test filter = server_test.can_handle_https_requests  
14: [==========] Running 1 test from 1 test suite.  
14: [----------] Global test environment set-up.  
14: [----------] 1 test from server_test  
14: [ RUN      ] server_test.can_handle_https_requests  
14: [TCP Listener] Waiting for server registration ...  
14: [HTTP Listener] Waiting for server registration ...  
14: [Signals] Ready ...   
14: (Boost.Redis) run-all-op: resolve addresses 172.17.0.3:6379  
14: (Boost.Redis) run-all-op: connected to endpoint 172.17.0.3:6379  
14: (Boost.Redis) writer-op: 117 bytes written.  
14: (Boost.Redis) reader-op: 271 bytes read.  
14: (Boost.Redis) hello-op: Success  
14: subscribe channel 1  
14: [Server Registration] Ready ...   
14: [TCP Listener] Acceptor was created ...   
14: [HTTP Listener] Acceptor was created ...   
14: (Boost.Redis) writer-op: 32 bytes written.  
14: (Boost.Redis) reader-op: 18 bytes read.  
14: [       OK ] server_test.can_handle_https_requests (2112 ms)  
14: [----------] 1 test from server_test (2112 ms total)  
14:   
14: [----------] Global test environment tear-down  
14: [==========] 1 test from 1 test suite ran. (2112 ms total)  
14: [  PASSED  ] 1 test.  
14/26 Test #14: server_test.can_handle_https_requests ..........................................   Passed    2.12 sec  
test 15  
      Start 15: server_test.can_handle_options_requests  
  
15: Test command: /builds/zentrack/server/build/tests "--gtest_filter=server_test.can_handle_options_requests" "--gtest_also_run_disabled_tests"  
15: Working Directory: /builds/zentrack/server/build  
15: Test timeout computed to be: 10000000  
15: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
15: Note: Google Test filter = server_test.can_handle_options_requests  
15: [==========] Running 1 test from 1 test suite.  
15: [----------] Global test environment set-up.  
15: [----------] 1 test from server_test  
15: [ RUN      ] server_test.can_handle_options_requests  
15: [TCP Listener] Waiting for server registration ...  
15: [HTTP Listener] Waiting for server registration ...  
15: [Signals] Ready ...   
15: (Boost.Redis) run-all-op: resolve addresses 172.17.0.3:6379  
15: (Boost.Redis) run-all-op: connected to endpoint 172.17.0.3:6379  
15: (Boost.Redis) writer-op: 117 bytes written.  
15: (Boost.Redis) reader-op: 271 bytes read.  
15: (Boost.Redis) hello-op: Success  
15: subscribe channel 1  
15: [Server Registration] Ready ...   
15: [HTTP Listener] Acceptor was created ...   
15: [TCP Listener] Acceptor was created ...   
15: (Boost.Redis) writer-op: 32 bytes written.  
15: (Boost.Redis) reader-op: 18 bytes read.  
15: [       OK ] server_test.can_handle_options_requests (2104 ms)  
15: [----------] 1 test from server_test (2104 ms total)  
15:   
15: [----------] Global test environment tear-down  
15: [==========] 1 test from 1 test suite ran. (2104 ms total)  
15: [  PASSED  ] 1 test.  
15/26 Test #15: server_test.can_handle_options_requests ........................................   Passed    2.11 sec  
test 16  
      Start 16: server_test.can_handle_not_found_requests  
  
16: Test command: /builds/zentrack/server/build/tests "--gtest_filter=server_test.can_handle_not_found_requests" "--gtest_also_run_disabled_tests"  
16: Working Directory: /builds/zentrack/server/build  
16: Test timeout computed to be: 10000000  
16: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
16: Note: Google Test filter = server_test.can_handle_not_found_requests  
16: [==========] Running 1 test from 1 test suite.  
16: [----------] Global test environment set-up.  
16: [----------] 1 test from server_test  
16: [ RUN      ] server_test.can_handle_not_found_requests  
16: [TCP Listener] Waiting for server registration ...  
16: [HTTP Listener] Waiting for server registration ...  
16: [Signals] Ready ...   
16: (Boost.Redis) run-all-op: resolve addresses 172.17.0.3:6379  
16: (Boost.Redis) run-all-op: connected to endpoint 172.17.0.3:6379  
16: (Boost.Redis) writer-op: 117 bytes written.  
16: (Boost.Redis) reader-op: 271 bytes read.  
16: (Boost.Redis) hello-op: Success  
16: subscribe channel 1  
16: [Server Registration] Ready ...   
16: [TCP Listener] Acceptor was created ...   
16: [HTTP Listener] Acceptor was created ...   
16: (Boost.Redis) writer-op: 32 bytes written.  
16: (Boost.Redis) reader-op: 18 bytes read.  
16: [       OK ] server_test.can_handle_not_found_requests (2104 ms)  
16: [----------] 1 test from server_test (2104 ms total)  
16:   
16: [----------] Global test environment tear-down  
16: [==========] 1 test from 1 test suite ran. (2104 ms total)  
16: [  PASSED  ] 1 test.  
16/26 Test #16: server_test.can_handle_not_found_requests ......................................   Passed    2.11 sec  
test 17  
      Start 17: server_test.can_handle_auth_requests  
  
17: Test command: /builds/zentrack/server/build/tests "--gtest_filter=server_test.can_handle_auth_requests" "--gtest_also_run_disabled_tests"  
17: Working Directory: /builds/zentrack/server/build  
17: Test timeout computed to be: 10000000  
17: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
17: Note: Google Test filter = server_test.can_handle_auth_requests  
17: [==========] Running 1 test from 1 test suite.  
17: [----------] Global test environment set-up.  
17: [----------] 1 test from server_test  
17: [ RUN      ] server_test.can_handle_auth_requests  
17: [TCP Listener] Waiting for server registration ...  
17: [HTTP Listener] Waiting for server registration ...  
17: [Signals] Ready ...   
17: (Boost.Redis) run-all-op: resolve addresses 172.17.0.3:6379  
17: (Boost.Redis) run-all-op: connected to endpoint 172.17.0.3:6379  
17: (Boost.Redis) writer-op: 117 bytes written.  
17: (Boost.Redis) reader-op: 271 bytes read.  
17: (Boost.Redis) hello-op: Success  
17: subscribe channel 1  
17: [Server Registration] Ready ...   
17: [HTTP Listener] Acceptor was created ...   
17: [TCP Listener] Acceptor was created ...   
17: (Boost.Redis) writer-op: 32 bytes written.  
17: (Boost.Redis) reader-op: 18 bytes read.  
17: [       OK ] server_test.can_handle_auth_requests (2356 ms)  
17: [----------] 1 test from server_test (2356 ms total)  
17:   
17: [----------] Global test environment tear-down  
17: [==========] 1 test from 1 test suite ran. (2356 ms total)  
17: [  PASSED  ] 1 test.  
17/26 Test #17: server_test.can_handle_auth_requests ...........................................   Passed    2.36 sec  
test 18  
      Start 18: server_test.can_handle_user_requests  
  
18: Test command: /builds/zentrack/server/build/tests "--gtest_filter=server_test.can_handle_user_requests" "--gtest_also_run_disabled_tests"  
18: Working Directory: /builds/zentrack/server/build  
18: Test timeout computed to be: 10000000  
18: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
18: Note: Google Test filter = server_test.can_handle_user_requests  
18: [==========] Running 1 test from 1 test suite.  
18: [----------] Global test environment set-up.  
18: [----------] 1 test from server_test  
18: [ RUN      ] server_test.can_handle_user_requests  
18: [TCP Listener] Waiting for server registration ...  
18: [HTTP Listener] Waiting for server registration ...  
18: [Signals] Ready ...   
18: (Boost.Redis) run-all-op: resolve addresses 172.17.0.3:6379  
18: (Boost.Redis) run-all-op: connected to endpoint 172.17.0.3:6379  
18: (Boost.Redis) writer-op: 117 bytes written.  
18: (Boost.Redis) reader-op: 271 bytes read.  
18: (Boost.Redis) hello-op: Success  
18: subscribe channel 1  
18: [Server Registration] Ready ...   
18: [TCP Listener] Acceptor was created ...   
18: [HTTP Listener] Acceptor was created ...   
18: (Boost.Redis) writer-op: 32 bytes written.  
18: (Boost.Redis) reader-op: 18 bytes read.  
18: [       OK ] server_test.can_handle_user_requests (2355 ms)  
18: [----------] 1 test from server_test (2355 ms total)  
18:   
18: [----------] Global test environment tear-down  
18: [==========] 1 test from 1 test suite ran. (2355 ms total)  
18: [  PASSED  ] 1 test.  
18/26 Test #18: server_test.can_handle_user_requests ...........................................   Passed    2.36 sec  
test 19  
      Start 19: server_test.can_accept_websocket_connections  
  
19: Test command: /builds/zentrack/server/build/tests "--gtest_filter=server_test.can_accept_websocket_connections" "--gtest_also_run_disabled_tests"  
19: Working Directory: /builds/zentrack/server/build  
19: Test timeout computed to be: 10000000  
19: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
19: Note: Google Test filter = server_test.can_accept_websocket_connections  
19: [==========] Running 1 test from 1 test suite.  
19: [----------] Global test environment set-up.  
19: [----------] 1 test from server_test  
19: [ RUN      ] server_test.can_accept_websocket_connections  
19: [TCP Listener] Waiting for server registration ...  
19: [HTTP Listener] Waiting for server registration ...  
19: [Signals] Ready ...   
19: (Boost.Redis) run-all-op: resolve addresses 172.17.0.3:6379  
19: (Boost.Redis) run-all-op: connected to endpoint 172.17.0.3:6379  
19: (Boost.Redis) writer-op: 117 bytes written.  
19: (Boost.Redis) reader-op: 271 bytes read.  
19: (Boost.Redis) hello-op: Success  
19: subscribe channel 1  
19: [Server Registration] Ready ...   
19: [TCP Listener] Acceptor was created ...   
19: [HTTP Listener] Acceptor was created ...   
19: (Boost.Redis) writer-op: 32 bytes written.  
19: (Boost.Redis) reader-op: 18 bytes read.  
19: [       OK ] server_test.can_accept_websocket_connections (2104 ms)  
19: [----------] 1 test from server_test (2104 ms total)  
19:   
19: [----------] Global test environment tear-down  
19: [==========] 1 test from 1 test suite ran. (2105 ms total)  
19: [  PASSED  ] 1 test.  
19/26 Test #19: server_test.can_accept_websocket_connections ...................................   Passed    2.11 sec  
test 20  
      Start 20: server_test.can_handle_websocket_messages  
  
20: Test command: /builds/zentrack/server/build/tests "--gtest_filter=server_test.can_handle_websocket_messages" "--gtest_also_run_disabled_tests"  
20: Working Directory: /builds/zentrack/server/build  
20: Test timeout computed to be: 10000000  
20: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
20: Note: Google Test filter = server_test.can_handle_websocket_messages  
20: [==========] Running 1 test from 1 test suite.  
20: [----------] Global test environment set-up.  
20: [----------] 1 test from server_test  
20: [ RUN      ] server_test.can_handle_websocket_messages  
20: [TCP Listener] Waiting for server registration ...  
20: [HTTP Listener] Waiting for server registration ...  
20: [Signals] Ready ...   
20: (Boost.Redis) run-all-op: resolve addresses 172.17.0.3:6379  
20: (Boost.Redis) run-all-op: connected to endpoint 172.17.0.3:6379  
20: (Boost.Redis) writer-op: 117 bytes written.  
20: (Boost.Redis) reader-op: 271 bytes read.  
20: (Boost.Redis) hello-op: Success  
20: subscribe channel 1  
20: [Server Registration] Ready ...   
20: [TCP Listener] Acceptor was created ...   
20: [HTTP Listener] Acceptor was created ...   
20: (Boost.Redis) writer-op: 32 bytes written.  
20: (Boost.Redis) reader-op: 18 bytes read.  
20: [       OK ] server_test.can_handle_websocket_messages (2105 ms)  
20: [----------] 1 test from server_test (2105 ms total)  
20:   
20: [----------] Global test environment tear-down  
20: [==========] 1 test from 1 test suite ran. (2105 ms total)  
20: [  PASSED  ] 1 test.  
20/26 Test #20: server_test.can_handle_websocket_messages ......................................   Passed    2.11 sec  
test 21  
      Start 21: server_test.can_validate_websocket_messages  
  
21: Test command: /builds/zentrack/server/build/tests "--gtest_filter=server_test.can_validate_websocket_messages" "--gtest_also_run_disabled_tests"  
21: Working Directory: /builds/zentrack/server/build  
21: Test timeout computed to be: 10000000  
21: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
21: Note: Google Test filter = server_test.can_validate_websocket_messages  
21: [==========] Running 1 test from 1 test suite.  
21: [----------] Global test environment set-up.  
21: [----------] 1 test from server_test  
21: [ RUN      ] server_test.can_validate_websocket_messages  
21: [TCP Listener] Waiting for server registration ...  
21: [HTTP Listener] Waiting for server registration ...  
21: [Signals] Ready ...   
21: (Boost.Redis) run-all-op: resolve addresses 172.17.0.3:6379  
21: (Boost.Redis) run-all-op: connected to endpoint 172.17.0.3:6379  
21: (Boost.Redis) writer-op: 117 bytes written.  
21: (Boost.Redis) reader-op: 271 bytes read.  
21: (Boost.Redis) hello-op: Success  
21: subscribe channel 1  
21: [Server Registration] Ready ...   
21: [TCP Listener] Acceptor was created ...   
21: [HTTP Listener] Acceptor was created ...   
21: (Boost.Redis) writer-op: 32 bytes written.  
21: (Boost.Redis) reader-op: 18 bytes read.  
21: [       OK ] server_test.can_validate_websocket_messages (2104 ms)  
21: [----------] 1 test from server_test (2104 ms total)  
21:   
21: [----------] Global test environment tear-down  
21: [==========] 1 test from 1 test suite ran. (2104 ms total)  
21: [  PASSED  ] 1 test.  
21/26 Test #21: server_test.can_validate_websocket_messages ....................................   Passed    2.11 sec  
test 22  
      Start 22: protocol_test.parse_records_sample_1  
  
22: Test command: /builds/zentrack/server/build/tests "--gtest_filter=protocol_test.parse_records_sample_1" "--gtest_also_run_disabled_tests"  
22: Working Directory: /builds/zentrack/server/build  
22: Test timeout computed to be: 10000000  
22: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
22: Note: Google Test filter = protocol_test.parse_records_sample_1  
22: [==========] Running 1 test from 1 test suite.  
22: [----------] Global test environment set-up.  
22: [----------] 1 test from protocol_test  
22: [ RUN      ] protocol_test.parse_records_sample_1  
22: [       OK ] protocol_test.parse_records_sample_1 (0 ms)  
22: [----------] 1 test from protocol_test (0 ms total)  
22:   
22: [----------] Global test environment tear-down  
22: [==========] 1 test from 1 test suite ran. (0 ms total)  
22: [  PASSED  ] 1 test.  
22/26 Test #22: protocol_test.parse_records_sample_1 ...........................................   Passed    0.01 sec  
test 23  
      Start 23: protocol_test.parse_records_sample_2  
  
23: Test command: /builds/zentrack/server/build/tests "--gtest_filter=protocol_test.parse_records_sample_2" "--gtest_also_run_disabled_tests"  
23: Working Directory: /builds/zentrack/server/build  
23: Test timeout computed to be: 10000000  
23: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
23: Note: Google Test filter = protocol_test.parse_records_sample_2  
23: [==========] Running 1 test from 1 test suite.  
23: [----------] Global test environment set-up.  
23: [----------] 1 test from protocol_test  
23: [ RUN      ] protocol_test.parse_records_sample_2  
23: [       OK ] protocol_test.parse_records_sample_2 (0 ms)  
23: [----------] 1 test from protocol_test (0 ms total)  
23:   
23: [----------] Global test environment tear-down  
23: [==========] 1 test from 1 test suite ran. (0 ms total)  
23: [  PASSED  ] 1 test.  
23/26 Test #23: protocol_test.parse_records_sample_2 ...........................................   Passed    0.01 sec  
test 24  
      Start 24: protocol_test.parse_records_sample_3  
  
24: Test command: /builds/zentrack/server/build/tests "--gtest_filter=protocol_test.parse_records_sample_3" "--gtest_also_run_disabled_tests"  
24: Working Directory: /builds/zentrack/server/build  
24: Test timeout computed to be: 10000000  
24: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
24: Note: Google Test filter = protocol_test.parse_records_sample_3  
24: [==========] Running 1 test from 1 test suite.  
24: [----------] Global test environment set-up.  
24: [----------] 1 test from protocol_test  
24: [ RUN      ] protocol_test.parse_records_sample_3  
24: [       OK ] protocol_test.parse_records_sample_3 (0 ms)  
24: [----------] 1 test from protocol_test (0 ms total)  
24:   
24: [----------] Global test environment tear-down  
24: [==========] 1 test from 1 test suite ran. (0 ms total)  
24: [  PASSED  ] 1 test.  
24/26 Test #24: protocol_test.parse_records_sample_3 ...........................................   Passed    0.01 sec  
test 25  
      Start 25: protocol_test.benchmark_parse_throughput  
  
25: Test command: /builds/zentrack/server/build/tests "--gtest_filter=protocol_test.benchmark_parse_throughput" "--gtest_also_run_disabled_tests"  
25: Working Directory: /builds/zentrack/server/build  
25: Test timeout computed to be: 10000000  
25: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
25: Note: Google Test filter = protocol_test.benchmark_parse_throughput  
25: [==========] Running 1 test from 1 test suite.  
25: [----------] Global test environment set-up.  
25: [----------] 1 test from protocol_test  
25: [ RUN      ] protocol_test.benchmark_parse_throughput  
25: [THROUGHPUT] parse(): 1.000 events using 7010 microseconds  
25: [       OK ] protocol_test.benchmark_parse_throughput (7 ms)  
25: [----------] 1 test from protocol_test (7 ms total)  
25:   
25: [----------] Global test environment tear-down  
25: [==========] 1 test from 1 test suite ran. (7 ms total)  
25: [  PASSED  ] 1 test.  
25/26 Test #25: protocol_test.benchmark_parse_throughput .......................................   Passed    0.01 sec  
test 26  
      Start 26: app_test.version  
  
26: Test command: /builds/zentrack/server/build/tests "--gtest_filter=app_test.version" "--gtest_also_run_disabled_tests"  
26: Working Directory: /builds/zentrack/server/build  
26: Test timeout computed to be: 10000000  
26: Running main() from /builds/zentrack/server/build/_deps/googletest-src/googletest/src/gtest_main.cc  
26: Note: Google Test filter = app_test.version  
26: [==========] Running 1 test from 1 test suite.  
26: [----------] Global test environment set-up.  
26: [----------] 1 test from app_test  
26: [ RUN      ] app_test.version  
26: [       OK ] app_test.version (0 ms)  
26: [----------] 1 test from app_test (0 ms total)  
26:   
26: [----------] Global test environment tear-down  
26: [==========] 1 test from 1 test suite ran. (0 ms total)  
26: [  PASSED  ] 1 test.  
26/26 Test #26: app_test.version ...............................................................   Passed    0.01 sec  
  
100% tests passed, 0 tests failed out of 26  
  
Total Test time (real) =  51.27 sec  
section_end:1753480716:step_script  
[0Ksection_start:1753480716:cleanup_file_variables  
[0K[0K[36;1mCleaning up project directory and file based variables[0;m[0;m  
section_end:1753480717:cleanup_file_variables  
[0K[32;1mJob succeeded[0;m  
```
