# #105 - Memory sanitizer warning on boost::uuids::to_string() call [Closed]

> Username: luckychess  
> Created at: 2019-05-14 10:01:15 UTC  
> Updated at: 2022-02-09 21:07:23 UTC  
> Closed at: 2022-02-09 21:07:22 UTC  
> Url: https://github.com/boostorg/uuid/issues/105  

Good afternoon!  
  
I have built a program that uses uuid library under msan and surprisingly got a warning. Here is a minimized example:  
```  
#include <iostream>  
#include <boost/uuid/uuid_generators.hpp>  
#include <boost/uuid/uuid_io.hpp>  
  
int main() {  
  boost::uuids::random_generator generator;  
  auto uuid = generator();  
  auto string_uuid = boost::uuids::to_string(uuid);  
  std::cout << string_uuid << std::endl;  
  
  return 0;  
}  
```  
The output is:  
```  
==10428==WARNING: MemorySanitizer: use-of-uninitialized-value  
    #0 0x4a6956 in boost::uuids::detail::to_char(unsigned long) /home/luckychess/src/boost/boost_1_70_0_msan/include/boost/uuid/uuid_io.hpp:132:9  
    #1 0x498877 in boost::uuids::to_string(boost::uuids::uuid const&) /home/luckychess/src/boost/boost_1_70_0_msan/include/boost/uuid/uuid_io.hpp:157:19  
    #2 0x497c1e in main /home/luckychess/src/uuid_test/main.cpp:8:22  
    #3 0x7f1ee403109a in __libc_start_main /build/glibc-B9XfQf/glibc-2.28/csu/../csu/libc-start.c:308:16  
    #4 0x41d449 in _start (/home/luckychess/src/uuid_test/build/uuid_test+0x41d449)  
  
  Uninitialized value was stored to memory at  
    #0 0x4a68b1 in boost::uuids::detail::to_char(unsigned long) /home/luckychess/src/boost/boost_1_70_0_msan/include/boost/uuid/uuid_io.hpp:131  
    #1 0x498877 in boost::uuids::to_string(boost::uuids::uuid const&) /home/luckychess/src/boost/boost_1_70_0_msan/include/boost/uuid/uuid_io.hpp:157:19  
    #2 0x497c1e in main /home/luckychess/src/uuid_test/main.cpp:8:22  
    #3 0x7f1ee403109a in __libc_start_main /build/glibc-B9XfQf/glibc-2.28/csu/../csu/libc-start.c:308:16  
  
  Uninitialized value was stored to memory at  
    #0 0x498801 in boost::uuids::to_string(boost::uuids::uuid const&) /home/luckychess/src/boost/boost_1_70_0_msan/include/boost/uuid/uuid_io.hpp:156:22  
    #1 0x497c1e in main /home/luckychess/src/uuid_test/main.cpp:8:22  
    #2 0x7f1ee403109a in __libc_start_main /build/glibc-B9XfQf/glibc-2.28/csu/../csu/libc-start.c:308:16  
  
  Uninitialized value was stored to memory at  
    #0 0x497b48 in main /home/luckychess/src/uuid_test/main.cpp:7:15  
    #1 0x7f1ee403109a in __libc_start_main /build/glibc-B9XfQf/glibc-2.28/csu/../csu/libc-start.c:308:16  
  
  Uninitialized value was stored to memory at  
    #0 0x422f8b in __msan_memcpy (/home/luckychess/src/uuid_test/build/uuid_test+0x422f8b)  
    #1 0x498031 in boost::uuids::random_generator_pure::operator()() /home/luckychess/src/boost/boost_1_70_0_msan/include/boost/uuid/random_generator.hpp:212:16  
    #2 0x497ac3 in main /home/luckychess/src/uuid_test/main.cpp:7:15  
    #3 0x7f1ee403109a in __libc_start_main /build/glibc-B9XfQf/glibc-2.28/csu/../csu/libc-start.c:308:16  
  
  Uninitialized value was created by an allocation of 'result' in the stack frame of function '_ZN5boost5uuids21random_generator_pureclEv'  
    #0 0x497dd0 in boost::uuids::random_generator_pure::operator()() /home/luckychess/src/boost/boost_1_70_0_msan/include/boost/uuid/random_generator.hpp:209  
  
SUMMARY: MemorySanitizer: use-of-uninitialized-value /home/luckychess/src/boost/boost_1_70_0_msan/include/boost/uuid/uuid_io.hpp:132:9 in boost::uuids::detail::to_char(unsigned long)  
Exiting  
```  
It may looks like `uuid` variable is not initialized properly but I did a check under debugger and it shows clear that all bytes in underlying uuid array are initialized so I'm confused. Also if I try to use `std::cout << uuid << std::endl;` (without `to_string` call) everything is fine.  
  
To reproduce this you need to build an instrumented version of libc++ and link this program against it. I used a manual from [here](https://github.com/google/sanitizers/wiki/MemorySanitizerLibcxxHowTo).  
  
Any ideas?

---

## Comment 1

> Username: jeking3  
> Created at: 2019-05-14 10:36:22 UTC  
> Url: https://github.com/boostorg/uuid/issues/105#issuecomment-492183801  

``Uninitialized value was created by an allocation of 'result'`` is a complaint that the default constructor is not initializing the bytes in the uuid structure.  This is intentional, as one is supposed to use a generator as this code does.  The entire content of the uuid structure is supposed to be overwritten with random data.  
  
Could you compile the test with `-DBOOST_UUID_RANDOM_PROVIDER_SHOW` and report the result so we know which generator the implementation selected on your system?  Thanks.

---

## Comment 2

> Username: luckychess  
> Created at: 2019-05-14 12:10:40 UTC  
> Url: https://github.com/boostorg/uuid/issues/105#issuecomment-492210934  

Yes, it looks like msan was unable to detect that `result` is actually initialized. But I'm still confused why msan blames on `to_string` call and not on `operator<<` - they both iterate over `result` in the same way:  
`for (uuid::const_iterator i_data = u.begin(); i_data!=u.end(); ++i_data, ++i) ...`  
  
About DBOOST_UUID_RANDOM_PROVIDER_SHOW - I'm sorry but I'm not sure how to use this option. I tried to run cmake with `-DBOOST_UUID_RANDOM_PROVIDER_SHOW=ON` flag and use `#define BOOST_UUID_RANDOM_PROVIDER_SHOW` in main.cpp but haven't got any output.  
  
Also my environment is Ubuntu 18.10 x64, clang 8.0, boost 1.70.

---

## Comment 3

> Username: jeking3  
> Created at: 2019-05-14 18:20:43 UTC  
> Updated at: 2019-05-14 19:15:32 UTC  
> Url: https://github.com/boostorg/uuid/issues/105#issuecomment-492351880  

Try `-DCMAKE_CXX_FLAGS_DEBUG="-DBOOST_UUID_RANDOM_PROVIDER_SHOW"` or something like that.   `-DBOOST_UUID_RANDOM_PROVIDER_SHOW` defines a cmake variable, not a C++ preprocessor definition.  
  
In your main.cpp if you put that #define before any #include it should have the same effect.

---

## Comment 4

> Username: luckychess  
> Created at: 2019-05-15 14:16:22 UTC  
> Url: https://github.com/boostorg/uuid/issues/105#issuecomment-492673593  

For some reason `#pragma message` produce no build output on my system (have no idea why) but I used -E cmake flag to get a preprocessed code and found this line:  
`#pragma message("BOOST_UUID_RANDOM_PROVIDER_NAME getrandom")`

---

## Comment 5

> Username: jeking3  
> Created at: 2022-02-09 21:07:22 UTC  
> Url: https://github.com/boostorg/uuid/issues/105#issuecomment-1034192925  

Unable to reproduce.  I added your code as a test in uuid then ran it:  
  
`b2 address-sanitizer=norecover undefined-sanitizer=norecover -d +4 test_issue_105 variant=debug address-model=64 cxxstd=11`  
  
There was no error reported.
