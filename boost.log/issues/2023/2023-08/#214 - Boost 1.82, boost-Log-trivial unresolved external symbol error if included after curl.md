# #214 - Boost 1.82, boost\Log\trivial unresolved external symbol error if included after curl [Closed]

> Username: StormLord07  
> Created at: 2023-08-07 21:10:48 UTC  
> Updated at: 2023-08-08 22:05:09 UTC  
> Closed at: 2023-08-08 22:05:09 UTC  
> Url: https://github.com/boostorg/log/issues/214  

CMakeLists.txt  
```  
cmake_minimum_required(VERSION 3.25)  
  
project(Bot)  
  
# Specify the required C++ standard  
set(CMAKE_CXX_STANDARD 20)  
set(CMAKE_CXX_STANDARD_REQUIRED True)  
  
set(CURL_INCLUDE_DIR "C:/Users/pasha/Desktop/curl-8.2.1/builds/libcurl-vc17-x64-release-static-ipv6-sspi-schannel/include")  
set(CURL_LIBRARY "C:/Users/pasha/Desktop/curl-8.2.1/builds/libcurl-vc17-x64-release-static-ipv6-sspi-schannel/lib/libcurl_a.lib")  
  
# Find required packages  
find_package(Boost 1.82 REQUIRED COMPONENTS log log_setup json)  
find_package(OpenSSL REQUIRED)  
find_package(CURL REQUIRED)  
  
add_definitions(-DCURL_STATICLIB)  
add_definitions(-DBOOST_LOG_STATIC_LINK)  
  
# Include directories for the packages  
include_directories(${Boost_INCLUDE_DIRS})  
include_directories(${OPENSSL_INCLUDE_DIR})  
include_directories(${CURL_INCLUDE_DIRS})  
  
# Define the executable or library and its sources  
add_executable(Bot main2.cpp)  
  
# Link the libraries to your project  
target_link_libraries(Bot  
    ${Boost_LIBRARIES}  
    OpenSSL::SSL OpenSSL::Crypto  
    ${CURL_LIBRARIES}  
    Normaliz.lib  
    Wldap32.lib  
    Crypt32.lib  
)  
```  
main2.cpp (minimum code to reproduce)  
```  
#include <curl/curl.h>  
#include <boost/log/trivial.hpp>  
  
int main() {  
    BOOST_LOG_TRIVIAL(info) << "info";  
    return 0;  
}  
```  
error  
```main2.obj : error LNK2019: unresolved external symbol "public: static void __cdecl boost::log::v2s_mt_nt62::record_view::public_data::destroy(struct boost::log::v2  
s_mt_nt62::record_view::public_data const *)" (?destroy@public_data@record_view@v2s_mt_nt62@log@boost@@SAXPEBU12345@@Z) in function "public: __cdecl boost::log::v2s_mt_nt62::rec  
ord::~record(void)" (??1record@v2s_mt_nt62@log@boost@@QEAA@XZ). [C:\Users\pasha\Desktop\Bot\Bot\build\Bot.vcxproj]  
main2.obj : error LNK2019: unresolved external symbol "public: bool __cdecl boost::log::v2s_mt_nt62::core::get_logging_enabled(void)const " (?get_logging_enabled@c   
ore@v2s_mt_nt62@log@boost@@QEBA_NXZ) in function main. [C:\Users\pasha\Desktop\Bot\Bot\build\Bot.vcxproj]  
main2.obj : error LNK2019: unresolved external symbol "public: class boost::log::v2s_mt_nt62::record __cdecl boost::log::v2s_mt_nt62::core::open_record(class boost   
::log::v2s_mt_nt62::attribute_set const &)" (?open_record@core@v2s_mt_nt62@log@boost@@QEAA?AVrecord@234@AEBVattribute_set@234@@Z) in function main. [C:\Users\pasha\Desktop\Bot\Bot\build\Bot.vcxproj]  
main2.obj : error LNK2019: unresolved external symbol "private: void __cdecl boost::log::v2s_mt_nt62::core::push_record_move(class boost::log::v2s_mt_nt62::record    
&)" (?push_record_move@core@v2s_mt_nt62@log@boost@@AEAAXAEAVrecord@234@@Z) in function "public: __cdecl boost::log::v2s_mt_nt62::aux::record_pump<class boost::log::v2s_mt_nt62::   
sources::severity_logger_mt<enum boost::log::v2s_mt_nt62::trivial::severity_level> >::~record_pump<class boost::log::v2s_mt_nt62::sources::severity_logger_mt<enum boost::log::   
_mt_nt62@log@boost@@QEAA@XZ). [C:\Users\pasha\Desktop\Bot\Bot\build\Bot.vcxproj]  
main2.obj : error LNK2019: unresolved external symbol "unsigned __int64 & __cdecl boost::log::v2s_mt_nt62::sources::aux::get_severity_level(void)" (?get_severity_l   
evel@aux@sources@v2s_mt_nt62@log@boost@@YAAEA_KXZ) in function main. [C:\Users\pasha\Desktop\Bot\Bot\build\Bot.vcxproj]  
main2.obj : error LNK2019: unresolved external symbol "public: static class boost::log::v2s_mt_nt62::sources::severity_logger_mt<enum boost::log::v2s_mt_nt62::triv   
ial::severity_level> & __cdecl boost::log::v2s_mt_nt62::trivial::logger::get(void)" (?get@logger@trivial@v2s_mt_nt62@log@boost@@SAAEAV?$severity_logger_mt@W4severity_level@tri   
vial@v2s_mt_nt62@log@boost@@@sources@345@XZ) in function main. [C:\Users\pasha\Desktop\Bot\Bot\build\Bot.vcxproj]  
main2.obj : error LNK2019: unresolved external symbol "public: static struct boost::log::v2s_mt_nt62::aux::stream_provider<char>::stream_compound * __cdecl boost::   
log::v2s_mt_nt62::aux::stream_provider<char>::allocate_compound(class boost::log::v2s_mt_nt62::record &)" (?allocate_compound@?$stream_provider@D@aux@v2s_mt_nt62@log@boost@@SA   
PEAUstream_compound@12345@AEAVrecord@345@@Z) in function main. [C:\Users\pasha\Desktop\Bot\Bot\build\Bot.vcxproj]  
main2.obj : error LNK2019: unresolved external symbol "public: static void __cdecl boost::log::v2s_mt_nt62::aux::stream_provider<char>::release_compound(struct boo   
st::log::v2s_mt_nt62::aux::stream_provider<char>::stream_compound *)" (?release_compound@?$stream_provider@D@aux@v2s_mt_nt62@log@boost@@SAXPEAUstream_compound@12345@@Z) в функ   
ции "public: __cdecl boost::log::v2s_mt_nt62::aux::record_pump<class boost::log::v2s_mt_nt62::sources::severity_logger_mt<enum boost::log::v2s_mt_nt62::trivial::severity_level   
> >::~record_pump<class boost::log::v2s_mt_nt62::sources::severity_logger_mt<enum boost::log::v2s_mt_nt62::trivial::severity_level> >(void)" (??1?$record_pump@V?$severity_logg   
er_mt@W4severity_level@trivial@v2s_mt_nt62@log@boost@@@sources@v2s_mt_nt62@log@boost@@@aux@v2s_mt_nt62@log@boost@@QEAA@XZ). [C:\Users\pasha\Desktop\Bot\Bot\build\Bot.vcxproj]  
```  
  
cmake logs:  
  
```  
PS C:\Users\pasha\Desktop\Bot\Bot> cmake . -B build  
-- Building for: Visual Studio 17 2022  
-- Selecting Windows SDK version 10.0.22000.0 to target Windows 10.0.19045.  
-- The C compiler identification is MSVC 19.35.32217.1  
-- The CXX compiler identification is MSVC 19.35.32217.1  
-- Detecting C compiler ABI info  
-- Detecting C compiler ABI info - done  
-- Check for working C compiler: C:/Program Files (x86)/Microsoft Visual Studio/2022/BuildTools/VC/Tools/MSVC/14.35.32215/bin/Hostx64/x64/cl.exe - skipped  
-- Detecting C compile features  
-- Detecting C compile features - done  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: C:/Program Files (x86)/Microsoft Visual Studio/2022/BuildTools/VC/Tools/MSVC/14.35.32215/bin/Hostx64/x64/cl.exe - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Found Boost: C:/Boost/lib/cmake/Boost-1.82.0/BoostConfig.cmake (found suitable version "1.82.0", minimum required is "1.82") found components: log log_setup json   
-- Found OpenSSL: optimized;C:/Program Files/OpenSSL-Win64/lib/VC/libcrypto64MD.lib;debug;C:/Program Files/OpenSSL-Win64/lib/VC/libcrypto64MDd.lib (found version "3.1.2")    
-- Found CURL: C:/Users/pasha/Desktop/curl-8.2.1/builds/libcurl-vc17-x64-release-static-ipv6-sspi-schannel/lib/libcurl_a.lib (found version "8.2.1")    
-- Configuring done (5.3s)  
-- Generating done (0.0s)  
-- Build files have been written to: C:/Users/pasha/Desktop/Bot/Bot/build  
```  
```  
PS C:\Users\pasha\Desktop\Bot\Bot> cmake --build .\build\ --config Release  
MSBuild version 17.5.1+f6fdcf537 for .NET Framework  
  
  1>Checking Build System  
  1>Building Custom Rule C:/Users/pasha/Desktop/Bot/Bot/CMakeLists.txt  
  main2.cpp  
main2.obj : error LNK2019: ссылка на неразрешенный внешний символ "public: static void __cdecl boost::log::v2s_mt_nt62::record_view::public_data::destroy(struct boost::log::v2  
s_mt_nt62::record_view::public_data const *)" (?destroy@public_data@record_view@v2s_mt_nt62@log@boost@@SAXPEBU12345@@Z) в функции "public: __cdecl boost::log::v2s_mt_nt62::rec  
ord::~record(void)" (??1record@v2s_mt_nt62@log@boost@@QEAA@XZ). [C:\Users\pasha\Desktop\Bot\Bot\build\Bot.vcxproj]  
main2.obj : error LNK2019: ссылка на неразрешенный внешний символ "public: bool __cdecl boost::log::v2s_mt_nt62::core::get_logging_enabled(void)const " (?get_logging_enabled@c   
ore@v2s_mt_nt62@log@boost@@QEBA_NXZ) в функции main. [C:\Users\pasha\Desktop\Bot\Bot\build\Bot.vcxproj]  
main2.obj : error LNK2019: ссылка на неразрешенный внешний символ "public: class boost::log::v2s_mt_nt62::record __cdecl boost::log::v2s_mt_nt62::core::open_record(class boost   
::log::v2s_mt_nt62::attribute_set const &)" (?open_record@core@v2s_mt_nt62@log@boost@@QEAA?AVrecord@234@AEBVattribute_set@234@@Z) в функции main. [C:\Users\pasha\Desktop\Bot\Bot\build\Bot.vcxproj]  
main2.obj : error LNK2019: ссылка на неразрешенный внешний символ "private: void __cdecl boost::log::v2s_mt_nt62::core::push_record_move(class boost::log::v2s_mt_nt62::record    
&)" (?push_record_move@core@v2s_mt_nt62@log@boost@@AEAAXAEAVrecord@234@@Z) в функции "public: __cdecl boost::log::v2s_mt_nt62::aux::record_pump<class boost::log::v2s_mt_nt62::   
sources::severity_logger_mt<enum boost::log::v2s_mt_nt62::trivial::severity_level> >::~record_pump<class boost::log::v2s_mt_nt62::sources::severity_logger_mt<enum boost::log::   
v2s_mt_nt62::trivial::severity_level> >(void)" (??1?$record_pump@V?$severity_logger_mt@W4severity_level@trivial@v2s_mt_nt62@log@boost@@@sources@v2s_mt_nt62@log@boost@@@aux@v2s   
_mt_nt62@log@boost@@QEAA@XZ). [C:\Users\pasha\Desktop\Bot\Bot\build\Bot.vcxproj]  
main2.obj : error LNK2019: ссылка на неразрешенный внешний символ "unsigned __int64 & __cdecl boost::log::v2s_mt_nt62::sources::aux::get_severity_level(void)" (?get_severity_l   
evel@aux@sources@v2s_mt_nt62@log@boost@@YAAEA_KXZ) в функции main. [C:\Users\pasha\Desktop\Bot\Bot\build\Bot.vcxproj]  
main2.obj : error LNK2019: ссылка на неразрешенный внешний символ "public: static class boost::log::v2s_mt_nt62::sources::severity_logger_mt<enum boost::log::v2s_mt_nt62::triv   
ial::severity_level> & __cdecl boost::log::v2s_mt_nt62::trivial::logger::get(void)" (?get@logger@trivial@v2s_mt_nt62@log@boost@@SAAEAV?$severity_logger_mt@W4severity_level@tri   
vial@v2s_mt_nt62@log@boost@@@sources@345@XZ) в функции main. [C:\Users\pasha\Desktop\Bot\Bot\build\Bot.vcxproj]  
main2.obj : error LNK2019: ссылка на неразрешенный внешний символ "public: static struct boost::log::v2s_mt_nt62::aux::stream_provider<char>::stream_compound * __cdecl boost::   
log::v2s_mt_nt62::aux::stream_provider<char>::allocate_compound(class boost::log::v2s_mt_nt62::record &)" (?allocate_compound@?$stream_provider@D@aux@v2s_mt_nt62@log@boost@@SA   
PEAUstream_compound@12345@AEAVrecord@345@@Z) в функции main. [C:\Users\pasha\Desktop\Bot\Bot\build\Bot.vcxproj]  
main2.obj : error LNK2019: ссылка на неразрешенный внешний символ "public: static void __cdecl boost::log::v2s_mt_nt62::aux::stream_provider<char>::release_compound(struct boo   
st::log::v2s_mt_nt62::aux::stream_provider<char>::stream_compound *)" (?release_compound@?$stream_provider@D@aux@v2s_mt_nt62@log@boost@@SAXPEAUstream_compound@12345@@Z) в функ   
ции "public: __cdecl boost::log::v2s_mt_nt62::aux::record_pump<class boost::log::v2s_mt_nt62::sources::severity_logger_mt<enum boost::log::v2s_mt_nt62::trivial::severity_level   
> >::~record_pump<class boost::log::v2s_mt_nt62::sources::severity_logger_mt<enum boost::log::v2s_mt_nt62::trivial::severity_level> >(void)" (??1?$record_pump@V?$severity_logg   
er_mt@W4severity_level@trivial@v2s_mt_nt62@log@boost@@@sources@v2s_mt_nt62@log@boost@@@aux@v2s_mt_nt62@log@boost@@QEAA@XZ). [C:\Users\pasha\Desktop\Bot\Bot\build\B   
ot.vcxproj]  
C:\Users\pasha\Desktop\Bot\Bot\build\Release\Bot.exe : fatal error LNK1120: неразрешенных внешних элементов: 8 [C:\Users\pasha\Desktop\Bot\Bot\build\Bo   
t.vcxproj]  
PS C:\Users\pasha\Desktop\Bot\Bot>   
```  
  
Platform: Windows 10 x64  
Visual Studio 17 2022  
CXX compiler - MSVC 19.35.32217.1  
Curl 8.2.1 built by x64 native tools for vs2022  
  
If any more info needed i'll be happy to provide

---

## Comment 1

> Username: Lastique  
> Created at: 2023-08-07 21:48:39 UTC  
> Url: https://github.com/boostorg/log/issues/214#issuecomment-1668622094  

Likely duplicates https://github.com/boostorg/log/issues/172.  
  
Check if Boost was built for Windows 8 or later. I suspect, libcurl headers may define `_WIN32_WINNT` (directly or indirectly) to something recent while Boost defaults to Windows 7 for your compiler.  
  
Inconsistent target Windows versions causes mismatch in Boost.Log namespace names in the built library and your code, which causes the missing symbols. You should ensure that Boost and your code target the same Windows version.

---

## Comment 2

> Username: StormLord07  
> Created at: 2023-08-08 08:19:37 UTC  
> Url: https://github.com/boostorg/log/issues/214#issuecomment-1669140765  

Maybe I did something wrong but I rebuilt boost with  
b2 clear (or was it clean, always mess them up)  
b2 -a --build-type=complete  define=_WIN32_WINNT=0x0A00   
b2 install  
  
now it works only after curl. I guessed wrong _WIN32_WINNT, thanks.

---

## Comment 3

> Username: Lastique  
> Created at: 2023-08-08 22:05:09 UTC  
> Url: https://github.com/boostorg/log/issues/214#issuecomment-1670368888  

> now it works only after curl  
  
You need to define `_WIN32_WINNT` consistently, whether or not you're including libcurl headers. Use it when you build Boost and add the same define to your project.
