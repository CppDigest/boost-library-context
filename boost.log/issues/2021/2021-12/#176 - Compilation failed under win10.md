# #176 - Compilation failed under win10 [Closed]

> Username: kqbi  
> Created at: 2021-12-20 03:36:40 UTC  
> Updated at: 2021-12-20 07:12:01 UTC  
> Closed at: 2021-12-20 07:12:00 UTC  
> Url: https://github.com/boostorg/log/issues/176  

use https://sourceforge.net/projects/boost/files/boost-binaries/1.78.0/ boost_1_78_0-msvc-14.1-64.exe , use static log library under vs2017 and _WIN32_WINNT=0x0A00  
  
Unresolved external symbol "public: static void * __cdecl boost::log::v2s_mt_nt62::attribute::impl::operator new(unsigned __int64)" (??2impl@attribute@v2s_mt_nt62@log@boost@@SAPEAX_K@Z)，The symbol is in the function "public: __cdecl boost::log::v2s_mt_nt62::sources::aux::severity_level<enum S_Log::severity_level>::severity_level<enum S_Log::severity_level>(void)" (??0?$severity_level@W40S_Log@@@aux@sources@v2s_mt_nt62@log@boost@@QEAA@XZ) Referenced in [D:\work\projects\github\S_Log\vsc_build\test\LogTest.vcxproj]  
  
  
```  
#ifdef _WIN32  
#include <Windows.h>  
#endif  
  
#include <boost/log/attributes.hpp>  
#include <boost/log/sinks.hpp>  
#include <boost/log/sinks/async_frontend.hpp>  
#include <boost/log/sinks/syslog_backend.hpp>  
#include <boost/log/trivial.hpp>  
#include <string>  
```  
but use https://sourceforge.net/projects/boost/files/boost-binaries/1.77.0/ boost_1_77_0-msvc-14.1-64.exe    is ok

---

## Comment 1

> Username: Aux  
> Created at: 2021-12-20 03:49:03 UTC  
> Url: https://github.com/boostorg/log/issues/176#issuecomment-997569438  

😡

---

## Comment 2

> Username: Lastique  
> Created at: 2021-12-20 07:12:00 UTC  
> Url: https://github.com/boostorg/log/issues/176#issuecomment-997656243  

Duplicates https://github.com/boostorg/log/issues/172.
