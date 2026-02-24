# #690 - Boost 1.78.0 & Ubuntu 20.04 produce:  undefined reference to `boost::json::parse(boost::basic_string_view<char, std::char_traits<char> > [Closed]

> Username: sebwr  
> Created at: 2022-03-15 11:26:47 UTC  
> Updated at: 2023-03-07 10:03:02 UTC  
> Closed at: 2022-03-15 14:44:19 UTC  
> Url: https://github.com/boostorg/json/issues/690  

### Version of Boost  
  
1.78.0  
  
### Steps necessary to reproduce the problem  
  
[Test which produces the Error ](https://github.com/sebwr/boost-json-test/tree/master)  
  
### All relevant compiler information  
  
The C compiler identification is GNU 9.3.0  
The CXX compiler identification is GNU 9.3.0  
  
  
#### Output  
```bash  
/usr/bin/ld: CMakeFiles/boostJsonTest.dir/main.cpp.o: in function `main':  
main.cpp:(.text+0x1de): undefined reference to `boost::json::parse(boost::basic_string_view<char, std::char_traits<char> >, boost::json::storage_ptr, boost::json::parse_options const&)'  
/usr/bin/ld: main.cpp:(.text+0x211): undefined reference to `boost::json::value::~value()'  
/usr/bin/ld: main.cpp:(.text+0x22f): undefined reference to `boost::json::object::~object()'  
/usr/bin/ld: main.cpp:(.text+0x292): undefined reference to `boost::json::value::~value()'  
/usr/bin/ld: CMakeFiles/boostJsonTest.dir/main.cpp.o: in function `boost::json::object::object(boost::json::object const&)':  
main.cpp:(.text._ZN5boost4json6objectC2ERKS1_[_ZN5boost4json6objectC5ERKS1_]+0x4a): undefined reference to `boost::json::object::object(boost::json::object const&, boost::json::storage_ptr)'  
/usr/bin/ld: CMakeFiles/boostJsonTest.dir/main.cpp.o: in function `boost::json::value::as_object()':  
main.cpp:(.text._ZN5boost4json5value9as_objectEv[_ZN5boost4json5value9as_objectEv]+0x66): undefined reference to `boost::json::detail::throw_invalid_argument(char const*, boost::source_location const&)'  
collect2: error: ld returned 1 exit status  
make[2]: *** [CMakeFiles/boostJsonTest.dir/build.make:84: boostJsonTest] Error 1  
make[1]: *** [CMakeFiles/Makefile2:76: CMakeFiles/boostJsonTest.dir/all] Error 2  
make: *** [Makefile:84: all] Error 2  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-03-15 11:33:47 UTC  
> Url: https://github.com/boostorg/json/issues/690#issuecomment-1067885596  

You use a pre-built shared library and you don't seem to link to it. That's why you get linking errors.

---

## Comment 2

> Username: sebwr  
> Created at: 2022-03-15 11:41:36 UTC  
> Url: https://github.com/boostorg/json/issues/690#issuecomment-1067891326  

I have used `libboost-all-dev` and after I realized, that boost-json is not yet a part of it, I built my own version with the instructions by [him](https://stackoverflow.com/a/41272796/10413428). Do I have to uninstall the version from the package manager first?

---

## Comment 3

> Username: grisumbras  
> Created at: 2022-03-15 11:50:15 UTC  
> Url: https://github.com/boostorg/json/issues/690#issuecomment-1067897776  

This has nothing to do with particular boost binaries. Nowhere in your build script you link your program to Boost.Json. You should add  
```  
target_link_libraries(boostJsonTest PRIVATE Boost::json)  
```

---

## Comment 4

> Username: sebwr  
> Created at: 2022-03-15 14:24:28 UTC  
> Url: https://github.com/boostorg/json/issues/690#issuecomment-1068048097  

Thank you very much. I missed that and now it is also clear that I am a beginner .   
You can close this "issue".

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-03-15 14:44:19 UTC  
> Url: https://github.com/boostorg/json/issues/690#issuecomment-1068069299  

Glad to help

---

## Comment 6

> Username: TryerGit  
> Created at: 2023-03-04 13:05:58 UTC  
> Updated at: 2023-03-04 13:29:34 UTC  
> Url: https://github.com/boostorg/json/issues/690#issuecomment-1454733935  

> This has nothing to do with particular boost binaries. Nowhere in your build script you link your program to Boost.Json. You should add  
>   
> ```  
> target_link_libraries(boostJsonTest PRIVATE Boost::json)  
> ```  
  
Hello,  
  
I seem to be having a similar issue. I was on an earlier version of boost which does not have boost::json (1.71.0). I had installed that via apt-get install. I removed that via `sudo apt-get -y --purge remove libboost-all-dev libboost-doc libboost-dev`  
  
Then, I essentially followed the steps at https://www.how2shout.com/linux/how-to-install-boost-c-on-ubuntu-20-04-or-22-04/ to install 1.81.0 which does have boost::json.  
  
Now, my code compiles fine. However, when linking, I obtain a series of undefined reference to `boost::json::object::empty_`  
  
The .cpp file that references boost::json is compiled thus in the makefile:  
  
	$(COMPILE.cc) -O2 -DNDEBUG -I../include  -std=c++14 -MMD -MP -MF "$@.d" -o ${OBJECTDIR}/_ext/31/AM.o ../src/AM.cpp  
  
If I understand correctly, what you have stated is for cmake building. I am not too familiar with cmake and how your suggestion would translate into the actual make command. Could you please let me know how I can change the above make command to link properly?  
  
Thank you.  
----  
ETA: the linker command option in my current makefile is empty:  
  
```  
LDLIBSOPTIONS=  
  
${CND_DISTDIR}/${CND_CONF}/${CND_PLATFORM}/linux: ${OBJECTFILES}  
	${MKDIR} -p ${CND_DISTDIR}/${CND_CONF}/${CND_PLATFORM}  
	${LINK.cc} -o ${CND_DISTDIR}/${CND_CONF}/${CND_PLATFORM}/linux ${OBJECTFILES} ${LDLIBSOPTIONS} -lm -lpthread -ldl  
```  
  
----  
  
ETA2: Got it to work header only based on this answer: https://stackoverflow.com/a/67148554/492307

---

## Comment 7

> Username: grisumbras  
> Created at: 2023-03-07 10:03:01 UTC  
> Url: https://github.com/boostorg/json/issues/690#issuecomment-1457886887  

You have essentially the same problem: you don't link to Boost.JSON. Add _at least_ `-lboost_json` to your linking rule. If it will also complain about undefined references to anything from `boost::container`, add `-lboost_container`.
