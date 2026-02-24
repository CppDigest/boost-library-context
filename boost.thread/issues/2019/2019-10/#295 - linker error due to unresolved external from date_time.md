# #295 - linker error due to unresolved external from date_time [Open]

> Username: alex-che  
> Created at: 2019-10-15 14:00:06 UTC  
> Updated at: 2019-10-15 14:00:06 UTC  
> Url: https://github.com/boostorg/thread/issues/295  

I'm using boost for my cross-platform project. On Windows, I build boost with the following flags:  
`b2.exe --with-atomic --with-chrono --with-context --with-date_time --with-filesystem --with-iostreams --with-log --with-program_options --with-regex --with-serialization --with-system --with-test --with-timer --with-thread define=BOOST_ATOMIC_LINK define=BOOST_CHRONO_LINK define=BOOST_CONTEXT_DYN_LINK define=BOOST_DATE_TIME_DYN_LINK define=BOOST_FILESYSTEM_DYN_LINK define=BOOST_IOSTREAMS_DYN_LINK define=BOOST_LOG_DYN_LINK define=BOOST_PROGRAM_OPTIONS_DYN_LINK define=BOOST_REGEX_DYN_LINK define=BOOST_SERIALIZATION_DYN_LINK define=BOOST_SYSTEM_DYN_LINK define=BOOST_TEST_DYN_LINK define=BOOST_THREAD_DYN_LINK define=BOOST_ASIO_DISABLE_THREAD_KEYWORD_EXTENSION define=BOOST_CONTEXT_SOURCE define=BOOST_THREAD_PROVIDES_GENERIC_SHARED_MUTEX_ON_WIN toolset=msvc-14.0  variant=release threading=multi link=shared runtime-link=shared stage`  
  
The build fails when creating boost_thread-vc140-mt-x32-1_71.dll with message  
`thread.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: __thiscall boost::gregorian::greg_month::greg_month(unsigned short)" (__imp_??0greg_month@gregorian@boost@@QAE@G@Z) referenced in function "class boost::posix_time::ptime __cdecl boost::posix_time::from_time_t(__int64)" (?from_time_t@posix_time@boost@@YA?AVptime@12@_J@Z)  
building\boost\bin.v2\libs\thread\build\msvc-14.0\release\threadapi-win32\threading-multi\boost_thread-vc140-mt-x32-1_71.dll : fatal error LNK1120: 1 unresolved externals`  
  
Is boost::date_time missing from boost::thread dependency?  
  
Adding the following to the bottom of the requirements rule in libs/thread/build/Jamfile.v2 fixes the issue:  
```      
    if <target-os>windows in $(properties)  
    {  
        result += <define>BOOST_THREAD_USES_DATE_TIME ;  
        result += <library>/boost/date_time//boost_date_time ;	  
    }  
```  
  
BTW, the same issue and fix worked for boost 1.54.
