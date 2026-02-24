# #18 - Fails to compile with boost super project develop branch [Closed]

> Username: mborland  
> Created at: 2024-01-18 10:51:19 UTC  
> Updated at: 2024-01-19 12:29:37 UTC  
> Closed at: 2024-01-19 12:29:37 UTC  
> Url: https://github.com/boostorg/inspect/issues/18  

This no longer compiles due to the use of deprecated and now removed APIs.  
  
```  
[1] gcc-9  
[2] gcc-9/release/link-static/visibility-hidden  
...patience...  
...found 3016 targets...  
...updating 47 targets...  
gcc.compile.c++ ../../../bin.v2/tools/inspect/build/gcc-9/release/link-static/ascii_check.o  
In file included from ../../../tools/inspect/ascii_check.hpp:12,  
                 from ../../../tools/inspect/ascii_check.cpp:11:  
../../../tools/inspect/inspector.hpp: In function 'std::string boost::inspect::relative_to(const boost::filesystem::path&, const boost::filesystem::path&)':  
../../../tools/inspect/inspector.hpp:95:12: error: 'class boost::filesystem::path' has no member named 'normalize'  
   95 |       base.normalize();  
      |            ^~~~~~~~~  
../../../tools/inspect/inspector.hpp:98:11: error: 'class boost::filesystem::path' has no member named 'normalize'  
   98 |       src.normalize();  
      |           ^~~~~~~~~  
  
    "g++"   -m64 -O3 -finline-functions -Wno-inline -Wall -std=gnu++14 -DBOOST_ALL_NO_LIB=1 -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_HAS_ICU=1 -DNDEBUG   -I"../../.." -I"/usr/include"  -c -o "../../../bin.v2/tools/inspect/build/gcc-9/release/link-static/ascii_check.o" "../../../tools/inspect/ascii_check.cpp"  
  
...failed gcc.compile.c++ ../../../bin.v2/tools/inspect/build/gcc-9/release/link-static/ascii_check.o...  
```  
  
The support for normalize was removed here:   
  
https://github.com/boostorg/filesystem/commit/5df060e95ca844fe91b29001b4ae22bdb65635c6

---

## Comment 1

> Username: pdimov  
> Created at: 2024-01-18 14:37:38 UTC  
> Url: https://github.com/boostorg/inspect/issues/18#issuecomment-1898602752  

Should be fixed by https://github.com/boostorg/inspect/commit/60696d345ffb90c88accfeacd94dd4ad3a40e82e.

---

## Comment 2

> Username: mborland  
> Created at: 2024-01-19 12:29:37 UTC  
> Url: https://github.com/boostorg/inspect/issues/18#issuecomment-1900330686  

Inspect CI runs are back to green: https://app.circleci.com/pipelines/github/boostorg/multiprecision/1376/workflows/54dad9bf-918e-4b8b-adaa-1ccbf7edba74/jobs/5520. Thanks for the quick fix.
