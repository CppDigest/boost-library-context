# #161 - Boost compiler error [Closed]

> Username: reshisanjay1  
> Created at: 2021-12-09 17:59:49 UTC  
> Updated at: 2021-12-26 21:53:19 UTC  
> Closed at: 2021-12-26 21:53:19 UTC  
> Url: https://github.com/boostorg/interprocess/issues/161  

Hi,  
In boost 1.78.0 I get the following compile error. Any thoughts on what could be going wrong?  
  
My configuration:  
+ gcc -dumpmachine  
[0mx86_64-alpine-linux-musl  
+ gcc --version  
[0mgcc (Alpine 10.3.1_git20211027) 10.3.1 20211027  
  
```  
./bootstrap.sh \  
    --with-libraries=headers,filesystem,regex,system,date_time,serialization,program_options,log \  
    --without-icu \  
    --prefix=${install_prefix}  
      
 ./b2 -d2 toolset=gcc-arm architecture=arm variant=release link=shared threading=multi hardcode-dll-paths=true 'dll-path='\''$ORIGIN'\''' 'cxxflags=-fstack-protector -D_FORTIFY_SOURCE=2 -fPIC' install  
[0mPerforming configuration checks  
  
    - default address-model    : 64-bit [1]  
    - default architecture     : arm [1]  
    - has std::atomic_ref      : no [2]  
    - has statx                : no [2]  
    - has statx syscall        : yes [2]  
    - has init_priority attribute : yes [2]  
    - has stat::st_blksize     : yes [2]  
    - has stat::st_mtim        : yes [2]  
    - has stat::st_mtimensec   : no [2]  
    - has stat::st_mtimespec   : no [2]  
    - has stat::st_birthtim    : no [2]  
    - has stat::st_birthtimensec : no [2]  
    - has stat::st_birthtimespec : no [2]  
    - native atomic int32 supported : yes [2]  
    - native syslog supported  : yes [2]  
    - pthread supports robust mutexes : yes [2]  
    - has_icu builds           : no [2]  
    - lockfree boost::atomic_flag : yes [2]  
  
[1] gcc-arm  
[2] gcc-arm/release/threadapi-pthread/threading-multi/visibility-hidden  
  
Component configuration:  
  
    - atomic                   : not building  
    - chrono                   : not building  
    - container                : not building  
    - context                  : not building  
    - contract                 : not building  
    - coroutine                : not building  
    - date_time                : building  
    - exception                : not building  
    - fiber                    : not building  
    - filesystem               : building  
    - graph                    : not building  
    - graph_parallel           : not building  
    - headers                  : building  
    - iostreams                : not building  
    - json                     : not building  
    - locale                   : not building  
    - log                      : building  
    - math                     : not building  
    - mpi                      : not building  
    - nowide                   : not building  
    - program_options          : building  
    - python                   : not building  
    - random                   : not building  
    - regex                    : building  
    - serialization            : building  
    - stacktrace               : not building  
    - system                   : building  
    - test                     : not building  
    - thread                   : not building  
    - timer                    : not building  
    - type_erasure             : not building  
    - wave                     : not building  
```  
  
```  
  
  
list(APPEND _BOOST_WSERIALIZATION_DEPS serialization headers)  
  
  
  
boost-install.generate-cmake-variant- bin.v2/libs/serialization/build/gcc-10/release/threading-multi/visibility-hidden/libboost_wserialization-variant-shared.cmake  
  
  
  
  
  
text-action bin.v2/libs/serialization/build/gcc-10/release/threading-multi/visibility-hidden/libboost_wserialization-variant-shared.cmake  
  
  
  
    cat "/tmp/jam1a2324e0.000" > "bin.v2/libs/serialization/build/gcc-10/release/threading-multi/visibility-hidden/libboost_wserialization-variant-shared.cmake"  
  
  
  
common.copy /usr/local/lib/cmake/boost_wserialization-1.78.0/libboost_wserialization-variant-shared.cmake  
  
  
  
    cp "bin.v2/libs/serialization/build/gcc-10/release/threading-multi/visibility-hidden/libboost_wserialization-variant-shared.cmake"  "/usr/local/lib/cmake/boost_wserialization-1.78.0/libboost_wserialization-variant-shared.cmake"  
  
  
  
gcc.compile.c++ bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/text_multifile_backend.o  
  
  
  
    "g++"  -ftemplate-depth-1024 -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden -fno-strict-aliasing -fstack-protector -D_FORTIFY_SOURCE=2 -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_LOG_BUILDING_THE_LIB=1 -DBOOST_LOG_DLL -DBOOST_LOG_HAS_PTHREAD_MUTEX_ROBUST -DBOOST_LOG_USE_AVX2 -DBOOST_LOG_USE_NATIVE_SYSLOG -DBOOST_LOG_USE_SSSE3 -DBOOST_LOG_WITHOUT_DEBUG_OUTPUT -DBOOST_LOG_WITHOUT_EVENT_LOG -DBOOST_SPIRIT_USE_PHOENIX_V3=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_USE_DLL=1 -DNDEBUG -D_XOPEN_SOURCE=600 -D__STDC_CONSTANT_MACROS  -I"." -I"libs/log/src"  -c -o "bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/text_multifile_backend.o" "libs/log/src/text_multifile_backend.cpp"  
  
  
  
gcc.compile.c++ bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/process_name.o  
  
  
  
    "g++"  -ftemplate-depth-1024 -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden -fno-strict-aliasing -fstack-protector -D_FORTIFY_SOURCE=2 -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_LOG_BUILDING_THE_LIB=1 -DBOOST_LOG_DLL -DBOOST_LOG_HAS_PTHREAD_MUTEX_ROBUST -DBOOST_LOG_USE_AVX2 -DBOOST_LOG_USE_NATIVE_SYSLOG -DBOOST_LOG_USE_SSSE3 -DBOOST_LOG_WITHOUT_DEBUG_OUTPUT -DBOOST_LOG_WITHOUT_EVENT_LOG -DBOOST_SPIRIT_USE_PHOENIX_V3=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_USE_DLL=1 -DNDEBUG -D_XOPEN_SOURCE=600 -D__STDC_CONSTANT_MACROS  -I"." -I"libs/log/src"  -c -o "bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/process_name.o" "libs/log/src/process_name.cpp"  
  
  
  
common.mkdir bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/setup  
  
  
  
        mkdir -p "bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/setup"  
  
      
  
gcc.compile.c++ bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/dump_avx2.o  
  
  
  
    "g++"  -ftemplate-depth-1024 -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden -fno-strict-aliasing -fstack-protector -D_FORTIFY_SOURCE=2 -fPIC -mavx -mavx2 -fabi-version=0 -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_LOG_BUILDING_THE_LIB=1 -DBOOST_LOG_DLL -DBOOST_LOG_HAS_PTHREAD_MUTEX_ROBUST -DBOOST_LOG_USE_NATIVE_SYSLOG -DBOOST_LOG_WITHOUT_EVENT_LOG -DBOOST_SPIRIT_USE_PHOENIX_V3=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_USE_DLL=1 -DNDEBUG -D_XOPEN_SOURCE=600 -D__STDC_CONSTANT_MACROS  -I"." -I"libs/log/src"  -c -o "bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/dump_avx2.o" "libs/log/src/dump_avx2.cpp"  
  
  
  
gcc.compile.c++ bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/dump_ssse3.o  
  
  
  
    "g++"  -ftemplate-depth-1024 -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden -fno-strict-aliasing -fstack-protector -D_FORTIFY_SOURCE=2 -fPIC -msse -msse2 -msse3 -mssse3 -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_LOG_BUILDING_THE_LIB=1 -DBOOST_LOG_DLL -DBOOST_LOG_HAS_PTHREAD_MUTEX_ROBUST -DBOOST_LOG_USE_NATIVE_SYSLOG -DBOOST_LOG_WITHOUT_EVENT_LOG -DBOOST_SPIRIT_USE_PHOENIX_V3=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_USE_DLL=1 -DNDEBUG -D_XOPEN_SOURCE=600 -D__STDC_CONSTANT_MACROS  -I"." -I"libs/log/src"  -c -o "bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/dump_ssse3.o" "libs/log/src/dump_ssse3.cpp"  
  
  
  
gcc.compile.c++ bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/trivial.o  
  
  
  
    "g++"  -ftemplate-depth-1024 -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden -fno-strict-aliasing -fstack-protector -D_FORTIFY_SOURCE=2 -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_LOG_BUILDING_THE_LIB=1 -DBOOST_LOG_DLL -DBOOST_LOG_HAS_PTHREAD_MUTEX_ROBUST -DBOOST_LOG_USE_AVX2 -DBOOST_LOG_USE_NATIVE_SYSLOG -DBOOST_LOG_USE_SSSE3 -DBOOST_LOG_WITHOUT_DEBUG_OUTPUT -DBOOST_LOG_WITHOUT_EVENT_LOG -DBOOST_SPIRIT_USE_PHOENIX_V3=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_USE_DLL=1 -DNDEBUG -D_XOPEN_SOURCE=600 -D__STDC_CONSTANT_MACROS  -I"." -I"libs/log/src"  -c -o "bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/trivial.o" "libs/log/src/trivial.cpp"  
  
  
  
gcc.compile.c++ bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/setup/parser_utils.o  
  
  
  
    "g++"  -ftemplate-depth-1024 -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden -fno-strict-aliasing -fstack-protector -D_FORTIFY_SOURCE=2 -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_LOG_DYN_LINK=1 -DBOOST_LOG_HAS_PTHREAD_MUTEX_ROBUST -DBOOST_LOG_SETUP_BUILDING_THE_LIB=1 -DBOOST_LOG_SETUP_DLL -DBOOST_LOG_USE_AVX2 -DBOOST_LOG_USE_NATIVE_SYSLOG -DBOOST_LOG_USE_SSSE3 -DBOOST_LOG_WITHOUT_EVENT_LOG -DBOOST_SPIRIT_USE_PHOENIX_V3=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_USE_DLL=1 -DNDEBUG -D_XOPEN_SOURCE=600 -D__STDC_CONSTANT_MACROS  -I"." -I"libs/log/src"  -c -o "bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/setup/parser_utils.o" "libs/log/src/setup/parser_utils.cpp"  
  
  
  
gcc.compile.c++ bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/exceptions.o  
  
  
  
    "g++"  -ftemplate-depth-1024 -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden -fno-strict-aliasing -fstack-protector -D_FORTIFY_SOURCE=2 -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_LOG_BUILDING_THE_LIB=1 -DBOOST_LOG_DLL -DBOOST_LOG_HAS_PTHREAD_MUTEX_ROBUST -DBOOST_LOG_USE_AVX2 -DBOOST_LOG_USE_NATIVE_SYSLOG -DBOOST_LOG_USE_SSSE3 -DBOOST_LOG_WITHOUT_DEBUG_OUTPUT -DBOOST_LOG_WITHOUT_EVENT_LOG -DBOOST_SPIRIT_USE_PHOENIX_V3=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_USE_DLL=1 -DNDEBUG -D_XOPEN_SOURCE=600 -D__STDC_CONSTANT_MACROS  -I"." -I"libs/log/src"  -c -o "bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/exceptions.o" "libs/log/src/exceptions.cpp"  
  
  
  
gcc.compile.c++ bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/posix/ipc_reliable_message_queue.o  
  
  
  
    "g++"  -ftemplate-depth-1024 -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden -fno-strict-aliasing -fstack-protector -D_FORTIFY_SOURCE=2 -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_LOG_BUILDING_THE_LIB=1 -DBOOST_LOG_DLL -DBOOST_LOG_HAS_PTHREAD_MUTEX_ROBUST -DBOOST_LOG_USE_AVX2 -DBOOST_LOG_USE_NATIVE_SYSLOG -DBOOST_LOG_USE_SSSE3 -DBOOST_LOG_WITHOUT_DEBUG_OUTPUT -DBOOST_LOG_WITHOUT_EVENT_LOG -DBOOST_SPIRIT_USE_PHOENIX_V3=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_USE_DLL=1 -DNDEBUG -D_XOPEN_SOURCE=600 -D__STDC_CONSTANT_MACROS  -I"." -I"libs/log/src"  -c -o "bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/posix/ipc_reliable_message_queue.o" "libs/log/src/posix/ipc_reliable_message_queue.cpp"  
  
  
  
In file included from libs/log/src/posix/ipc_reliable_message_queue.cpp:53:  
  
./boost/interprocess/permissions.hpp:73:14: error: 'mode_t' in namespace '::' does not name a type  
  
   73 |    typedef ::mode_t    os_permissions_type;  
  
      |              ^~~~~~  
  
./boost/interprocess/permissions.hpp:75:4: error: 'os_permissions_type' does not name a type  
  
   75 |    os_permissions_type  m_perm;  
  
      |    ^~~~~~~~~~~~~~~~~~~  
  
./boost/interprocess/permissions.hpp:82:35: error: expected ')' before 'type'  
  
   82 |    permissions(os_permissions_type type) BOOST_NOEXCEPT  
  
      |               ~                   ^~~~~  
  
      |                                   )  
  
./boost/interprocess/permissions.hpp:117:25: error: 'os_permissions_type' has not been declared  
  
  117 |    void set_permissions(os_permissions_type perm) BOOST_NOEXCEPT  
  
      |                         ^~~~~~~~~~~~~~~~~~~  
  
./boost/interprocess/permissions.hpp:122:4: error: 'os_permissions_type' does not name a type  
  
  122 |    os_permissions_type get_permissions() const BOOST_NOEXCEPT  
  
      |    ^~~~~~~~~~~~~~~~~~~  
  
./boost/interprocess/permissions.hpp: In member function 'void boost::interprocess::permissions::set_default()':  
  
./boost/interprocess/permissions.hpp:100:7: error: 'm_perm' was not declared in this scope  
  
  100 |       m_perm = 0644;  
  
      |       ^~~~~~  
  
./boost/interprocess/permissions.hpp: In member function 'void boost::interprocess::permissions::set_unrestricted()':  
  
./boost/interprocess/permissions.hpp:111:7: error: 'm_perm' was not declared in this scope  
  
  111 |       m_perm = 0666;  
  
      |       ^~~~~~  
  
./boost/interprocess/permissions.hpp: In member function 'void boost::interprocess::permissions::set_permissions(int)':  
  
./boost/interprocess/permissions.hpp:118:7: error: 'm_perm' was not declared in this scope; did you mean 'perm'?  
  
  118 |    {  m_perm = perm; }  
  
      |       ^~~~~~  
  
      |       perm  
  
In file included from ./boost/interprocess/mapped_region.hpp:29,  
  
                 from libs/log/src/posix/ipc_reliable_message_queue.cpp:54:  
  
./boost/interprocess/detail/os_file_functions.hpp: In function 'boost::interprocess::file_handle_t boost::interprocess::ipcdetail::create_new_file(const char*, boost::interprocess::mode_t, const boost::interprocess::permissions&, bool)':  
  
./boost/interprocess/detail/os_file_functions.hpp:547:64: error: 'const class boost::interprocess::permissions' has no member named 'get_permissions'; did you mean 'set_permissions'?  
  
  547 |    int ret = ::open(name, ((int)mode) | O_EXCL | O_CREAT, perm.get_permissions());  
  
      |                                                                ^~~~~~~~~~~~~~~  
  
      |                                                                set_permissions  
  
./boost/interprocess/detail/os_file_functions.hpp:549:26: error: 'const class boost::interprocess::permissions' has no member named 'get_permissions'; did you mean 'set_permissions'?  
  
  549 |       ::fchmod(ret, perm.get_permissions());  
  
      |                          ^~~~~~~~~~~~~~~  
  
      |                          set_permissions  
  
./boost/interprocess/detail/os_file_functions.hpp: In function 'boost::interprocess::file_handle_t boost::interprocess::ipcdetail::create_or_open_file(const char*, boost::interprocess::mode_t, const boost::interprocess::permissions&, bool)':  
  
./boost/interprocess/detail/os_file_functions.hpp:562:63: error: 'const class boost::interprocess::permissions' has no member named 'get_permissions'; did you mean 'set_permissions'?  
  
  562 |       ret = ::open(name, ((int)mode) | O_EXCL | O_CREAT, perm.get_permissions());  
  
      |                                                               ^~~~~~~~~~~~~~~  
  
      |                                                               set_permissions  
  
./boost/interprocess/detail/os_file_functions.hpp:564:29: error: 'const class boost::interprocess::permissions' has no member named 'get_permissions'; did you mean 'set_permissions'?  
  
  564 |          ::fchmod(ret, perm.get_permissions());  
  
      |                             ^~~~~~~~~~~~~~~  
  
      |                             set_permissions  
  
In file included from libs/log/src/posix/ipc_reliable_message_queue.cpp:55:  
  
./boost/interprocess/shared_memory_object.hpp: In member function 'bool boost::interprocess::shared_memory_object::priv_open_or_create(boost::interprocess::ipcdetail::create_enum_t, const CharT*, boost::interprocess::mode_t, const boost::interprocess::permissions&)':  
  
./boost/interprocess/shared_memory_object.hpp:371:30: error: 'const class boost::interprocess::permissions' has no member named 'get_permissions'; did you mean 'set_permissions'?  
  
  371 |    ::mode_t unix_perm = perm.get_permissions();  
  
      |                              ^~~~~~~~~~~~~~~  
  
      |                              set_permissions  
  
libs/log/src/posix/ipc_reliable_message_queue.cpp: In constructor 'boost::log::v2_mt_posix::ipc::reliable_message_queue::implementation::implementation(boost::log::v2_mt_posix::open_mode::create_only_tag, const boost::log::v2_mt_posix::ipc::object_name&, uint32_t, boost::log::v2_mt_posix::ipc::reliable_message_queue::size_type, boost::log::v2_mt_posix::ipc::reliable_message_queue::overflow_policy, const boost::log::v2_mt_posix::permissions&)':  
  
libs/log/src/posix/ipc_reliable_message_queue.cpp:237:70: error: no matching function for call to 'boost::interprocess::permissions::permissions(boost::log::v2_mt_posix::permissions::native_type)'  
  
  237 |         boost::interprocess::permissions ipc_perms(perms.get_native());  
  
      |                                                                      ^  
  
In file included from libs/log/src/posix/ipc_reliable_message_queue.cpp:53:  
  
./boost/interprocess/permissions.hpp:89:4: note: candidate: 'boost::interprocess::permissions::permissions()'  
  
   89 |    permissions() BOOST_NOEXCEPT  
  
      |    ^~~~~~~~~~~  
  
./boost/interprocess/permissions.hpp:89:4: note:   candidate expects 0 arguments, 1 provided  
  
./boost/interprocess/permissions.hpp:66:7: note: candidate: 'constexpr boost::interprocess::permissions::permissions(const boost::interprocess::permissions&)'  
  
   66 | class permissions  
  
      |       ^~~~~~~~~~~  
  
./boost/interprocess/permissions.hpp:66:7: note:   no known conversion for argument 1 from 'boost::log::v2_mt_posix::permissions::native_type' {aka 'unsigned int'} to 'const boost::interprocess::permissions&'  
  
./boost/interprocess/permissions.hpp:66:7: note: candidate: 'constexpr boost::interprocess::permissions::permissions(boost::interprocess::permissions&&)'  
  
./boost/interprocess/permissions.hpp:66:7: note:   no known conversion for argument 1 from 'boost::log::v2_mt_posix::permissions::native_type' {aka 'unsigned int'} to 'boost::interprocess::permissions&&'  
  
libs/log/src/posix/ipc_reliable_message_queue.cpp: In constructor 'boost::log::v2_mt_posix::ipc::reliable_message_queue::implementation::implementation(boost::log::v2_mt_posix::open_mode::open_or_create_tag, const boost::log::v2_mt_posix::ipc::object_name&, uint32_t, boost::log::v2_mt_posix::ipc::reliable_message_queue::size_type, boost::log::v2_mt_posix::ipc::reliable_message_queue::overflow_policy, const boost::log::v2_mt_posix::permissions&)':  
  
libs/log/src/posix/ipc_reliable_message_queue.cpp:282:70: error: no matching function for call to 'boost::interprocess::permissions::permissions(boost::log::v2_mt_posix::permissions::native_type)'  
  
  282 |         boost::interprocess::permissions ipc_perms(perms.get_native());  
  
      |                                                                      ^  
  
In file included from libs/log/src/posix/ipc_reliable_message_queue.cpp:53:  
  
./boost/interprocess/permissions.hpp:89:4: note: candidate: 'boost::interprocess::permissions::permissions()'  
  
   89 |    permissions() BOOST_NOEXCEPT  
  
      |    ^~~~~~~~~~~  
  
./boost/interprocess/permissions.hpp:89:4: note:   candidate expects 0 arguments, 1 provided  
  
./boost/interprocess/permissions.hpp:66:7: note: candidate: 'constexpr boost::interprocess::permissions::permissions(const boost::interprocess::permissions&)'  
  
   66 | class permissions  
  
      |       ^~~~~~~~~~~  
  
./boost/interprocess/permissions.hpp:66:7: note:   no known conversion for argument 1 from 'boost::log::v2_mt_posix::permissions::native_type' {aka 'unsigned int'} to 'const boost::interprocess::permissions&'  
  
./boost/interprocess/permissions.hpp:66:7: note: candidate: 'constexpr boost::interprocess::permissions::permissions(boost::interprocess::permissions&&)'  
  
./boost/interprocess/permissions.hpp:66:7: note:   no known conversion for argument 1 from 'boost::log::v2_mt_posix::permissions::native_type' {aka 'unsigned int'} to 'boost::interprocess::permissions&&'  
  
...failed gcc.compile.c++ bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/posix/ipc_reliable_message_queue.o...  
  
gcc.compile.c++ bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/setup/init_from_stream.o  
  
  
  
    "g++"  -ftemplate-depth-1024 -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden -fno-strict-aliasing -fstack-protector -D_FORTIFY_SOURCE=2 -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_LOG_DYN_LINK=1 -DBOOST_LOG_HAS_PTHREAD_MUTEX_ROBUST -DBOOST_LOG_SETUP_BUILDING_THE_LIB=1 -DBOOST_LOG_SETUP_DLL -DBOOST_LOG_USE_AVX2 -DBOOST_LOG_USE_NATIVE_SYSLOG -DBOOST_LOG_USE_SSSE3 -DBOOST_LOG_WITHOUT_EVENT_LOG -DBOOST_SPIRIT_USE_PHOENIX_V3=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_USE_DLL=1 -DNDEBUG -D_XOPEN_SOURCE=600 -D__STDC_CONSTANT_MACROS  -I"." -I"libs/log/src"  -c -o "bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/setup/init_from_stream.o" "libs/log/src/setup/init_from_stream.cpp"  
  
  
  
gcc.compile.c++ bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/record_ostream.o  
  
  
  
    "g++"  -ftemplate-depth-1024 -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden -fno-strict-aliasing -fstack-protector -D_FORTIFY_SOURCE=2 -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_LOG_BUILDING_THE_LIB=1 -DBOOST_LOG_DLL -DBOOST_LOG_HAS_PTHREAD_MUTEX_ROBUST -DBOOST_LOG_USE_AVX2 -DBOOST_LOG_USE_NATIVE_SYSLOG -DBOOST_LOG_USE_SSSE3 -DBOOST_LOG_WITHOUT_DEBUG_OUTPUT -DBOOST_LOG_WITHOUT_EVENT_LOG -DBOOST_SPIRIT_USE_PHOENIX_V3=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_USE_DLL=1 -DNDEBUG -D_XOPEN_SOURCE=600 -D__STDC_CONSTANT_MACROS  -I"." -I"libs/log/src"  -c -o "bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/record_ostream.o" "libs/log/src/record_ostream.cpp"  
  
  
  
gcc.compile.c++ bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/spirit_encoding.o  
  
  
  
    "g++"  -ftemplate-depth-1024 -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden -fno-strict-aliasing -fstack-protector -D_FORTIFY_SOURCE=2 -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_LOG_BUILDING_THE_LIB=1 -DBOOST_LOG_DLL -DBOOST_LOG_HAS_PTHREAD_MUTEX_ROBUST -DBOOST_LOG_USE_AVX2 -DBOOST_LOG_USE_NATIVE_SYSLOG -DBOOST_LOG_USE_SSSE3 -DBOOST_LOG_WITHOUT_DEBUG_OUTPUT -DBOOST_LOG_WITHOUT_EVENT_LOG -DBOOST_SPIRIT_USE_PHOENIX_V3=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_USE_DLL=1 -DNDEBUG -D_XOPEN_SOURCE=600 -D__STDC_CONSTANT_MACROS  -I"." -I"libs/log/src"  -c -o "bin.v2/libs/log/build/gcc-10/release/threadapi-pthread/threading-multi/visibility-hidden/spirit_encoding.o" "libs/log/src/spirit_encoding.cpp"  
  
  
  
common.mkdir bin.v2/libs/log/build/install  
  
  
  
        mkdir -p "bin.v2/libs/log/build/install"  
 ```  
   
   
 Thanks

---

## Comment 1

> Username: reshisanjay1  
> Created at: 2021-12-12 16:35:36 UTC  
> Url: https://github.com/boostorg/interprocess/issues/161#issuecomment-991929273  

https://techoverflow.net/2019/06/20/how-to-fix-c-error-mode_t-undeclared/ ?
