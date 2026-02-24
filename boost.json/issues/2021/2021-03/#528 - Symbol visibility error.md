# #528 - Symbol visibility error? [Closed]

> Username: doganulus  
> Created at: 2021-03-11 20:39:50 UTC  
> Updated at: 2021-03-31 15:54:04 UTC  
> Closed at: 2021-03-31 15:53:54 UTC  
> Url: https://github.com/boostorg/json/issues/528  

Standalone build (shared lib + hidden visibility) invoked by the command   
```  
cmake .. -DBOOST_JSON_STANDALONE=ON -DBOOST_JSON_BUILD_TESTS=OFF -DBOOST_JSON_BUILD_FUZZERS=OFF -DBOOST_JSON_BUILD_BENCHMARKS=OFF -DBOOST_JSON_BUILD_EXAMPLES=ON -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_FLAGS="-fvisibility=hidden" -DBUILD_SHARED_LIBS=ON  
```  
gives undefined reference errors. Could be related to #496 or am I doing something wrong?  
  
Standalone static and shared with default visibility settings seem fine.  
  
Tested with the latest commit (e580ac).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-03-11 20:41:43 UTC  
> Url: https://github.com/boostorg/json/issues/528#issuecomment-797035037  

Do you want to post the exact compiler/linker output?

---

## Comment 2

> Username: doganulus  
> Created at: 2021-03-11 20:42:34 UTC  
> Url: https://github.com/boostorg/json/issues/528#issuecomment-797035522  

```  
/usr/bin/ld: CMakeFiles/validate.dir/validate.cpp.o: in function `validate(std::basic_string_view<char, std::char_traits<char> >)':  
validate.cpp:(.text+0xca): undefined reference to `boost::json::standalone::detail::stack::~stack()'  
/usr/bin/ld: validate.cpp:(.text+0x106): undefined reference to `boost::json::standalone::make_error_code(boost::json::standalone::error)'  
/usr/bin/ld: validate.cpp:(.text+0x15e): undefined reference to `boost::json::standalone::make_error_code(boost::json::standalone::error)'  
/usr/bin/ld: CMakeFiles/validate.dir/validate.cpp.o: in function `boost::json::standalone::basic_parser<null_parser::handler>::maybe_suspend(char const*, boost::json::standalone::basic_parser<null_parser::handler>::state)':  
validate.cpp:(.text._ZN5boost4json10standalone12basic_parserIN11null_parser7handlerEE13maybe_suspendEPKcNS5_5stateE[_ZN5boost4json10standalone12basic_parserIN11null_parser7handlerEE13maybe_suspendEPKcNS5_5stateE]+0x59): undefined reference to `boost::json::standalone::detail::stack::reserve(unsigned long)'  
/usr/bin/ld: CMakeFiles/validate.dir/validate.cpp.o: in function `boost::json::standalone::basic_parser<null_parser::handler>::suspend_or_fail(boost::json::standalone::basic_parser<null_parser::handler>::state)':  
validate.cpp:(.text._ZN5boost4json10standalone12basic_parserIN11null_parser7handlerEE15suspend_or_failENS5_5stateE[_ZN5boost4json10standalone12basic_parserIN11null_parser7handlerEE15suspend_or_failENS5_5stateE]+0x62): undefined reference to `boost::json::standalone::detail::stack::reserve(unsigned long)'  
/usr/bin/ld: CMakeFiles/validate.dir/validate.cpp.o: in function `boost::json::standalone::basic_parser<null_parser::handler>::suspend(char const*, boost::json::standalone::basic_parser<null_parser::handler>::state)':  
validate.cpp:(.text._ZN5boost4json10standalone12basic_parserIN11null_parser7handlerEE7suspendEPKcNS5_5stateE[_ZN5boost4json10standalone12basic_parserIN11null_parser7handlerEE7suspendEPKcNS5_5stateE]+0x4a): undefined reference to `boost::json::standalone::detail::stack::reserve(unsigned long)'  
/usr/bin/ld: CMakeFiles/validate.dir/validate.cpp.o: in function `boost::json::standalone::basic_parser<null_parser::handler>::fail(char const*, boost::json::standalone::error)':  
validate.cpp:(.text._ZN5boost4json10standalone12basic_parserIN11null_parser7handlerEE4failEPKcNS1_5errorE[_ZN5boost4json10standalone12basic_parserIN11null_parser7handlerEE4failEPKcNS1_5errorE]+0x11): undefined reference to `boost::json::standalone::make_error_code(boost::json::standalone::error)'  
/usr/bin/ld: CMakeFiles/validate.dir/validate.cpp.o: in function `boost::json::standalone::basic_parser<null_parser::handler>::maybe_suspend(char const*, boost::json::standalone::basic_parser<null_parser::handler>::state, boost::json::standalone::basic_parser<null_parser::handler>::number const&)':  
validate.cpp:(.text._ZN5boost4json10standalone12basic_parserIN11null_parser7handlerEE13maybe_suspendEPKcNS5_5stateERKNS5_6numberE[_ZN5boost4json10standalone12basic_parserIN11null_parser7handlerEE13maybe_suspendEPKcNS5_5stateERKNS5_6numberE]+0x69): undefined reference to `boost::json::standalone::detail::stack::reserve(unsigned long)'  
/usr/bin/ld: CMakeFiles/validate.dir/validate.cpp.o: in function `boost::json::standalone::basic_parser<null_parser::handler>::suspend(char const*, boost::json::standalone::basic_parser<null_parser::handler>::state, boost::json::standalone::basic_parser<null_parser::handler>::number const&)':  
validate.cpp:(.text._ZN5boost4json10standalone12basic_parserIN11null_parser7handlerEE7suspendEPKcNS5_5stateERKNS5_6numberE[_ZN5boost4json10standalone12basic_parserIN11null_parser7handlerEE7suspendEPKcNS5_5stateERKNS5_6numberE]+0x5a): undefined reference to `boost::json::standalone::detail::stack::reserve(unsigned long)'  
/usr/bin/ld: CMakeFiles/validate.dir/validate.cpp.o: in function `boost::json::standalone::basic_parser<null_parser::handler>::maybe_suspend(char const*, boost::json::standalone::basic_parser<null_parser::handler>::state, unsigned long)':  
validate.cpp:(.text._ZN5boost4json10standalone12basic_parserIN11null_parser7handlerEE13maybe_suspendEPKcNS5_5stateEm[_ZN5boost4json10standalone12basic_parserIN11null_parser7handlerEE13maybe_suspendEPKcNS5_5stateEm]+0x73): undefined reference to `boost::json::standalone::detail::stack::reserve(unsigned long)'  
/usr/bin/ld: CMakeFiles/validate.dir/validate.cpp.o: in function `boost::json::standalone::basic_parser<null_parser::handler>::suspend_or_fail(boost::json::standalone::basic_parser<null_parser::handler>::state, unsigned long)':  
validate.cpp:(.text._ZN5boost4json10standalone12basic_parserIN11null_parser7handlerEE15suspend_or_failENS5_5stateEm[_ZN5boost4json10standalone12basic_parserIN11null_parser7handlerEE15suspend_or_failENS5_5stateEm]+0x76): undefined reference to `boost::json::standalone::detail::stack::reserve(unsigned long)'  
/usr/bin/ld: CMakeFiles/validate.dir/validate.cpp.o: in function `validate(std::basic_string_view<char, std::char_traits<char> >) [clone .cold]':  
validate.cpp:(.text.unlikely+0x6): undefined reference to `boost::json::standalone::detail::stack::~stack()'  
collect2: error: ld returned 1 exit status  
make[2]: *** [example/CMakeFiles/validate.dir/build.make:85: example/validate] Error 1  
make[1]: *** [CMakeFiles/Makefile2:129: example/CMakeFiles/validate.dir/all] Error 2  
make: *** [Makefile:130: all] Error 2  
  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-03-11 20:45:09 UTC  
> Url: https://github.com/boostorg/json/issues/528#issuecomment-797036950  

What happens when you link to the library statically?

---

## Comment 4

> Username: doganulus  
> Created at: 2021-03-11 20:48:29 UTC  
> Url: https://github.com/boostorg/json/issues/528#issuecomment-797038861  

I have no problem with the static standalone build.

---

## Comment 5

> Username: grisumbras  
> Created at: 2021-03-12 15:12:32 UTC  
> Url: https://github.com/boostorg/json/issues/528#issuecomment-797552213  

This is actually documented:  
https://www.boost.org/doc/libs/1_75_0/libs/json/doc/html/json/overview.html#json.overview.requirements.standalone_shared_library  
  
But the question is why doesn't CMake build script do that by itself?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2021-03-12 16:24:45 UTC  
> Url: https://github.com/boostorg/json/issues/528#issuecomment-797599562  

> But the question is why doesn't CMake build script do that by itself?  
  
I think because it is not portable and there was not an obvious way to do it. Although I am not a cmake expert, so it is possible this might be fixed.

---

## Comment 7

> Username: grisumbras  
> Created at: 2021-03-12 16:29:05 UTC  
> Url: https://github.com/boostorg/json/issues/528#issuecomment-797602318  

There's a CMake macro for that, so should be doable.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2021-03-12 17:40:06 UTC  
> Url: https://github.com/boostorg/json/issues/528#issuecomment-797647245  

If you can get it to work, that would be great. Don't forget to update the README and the qbk file :)
