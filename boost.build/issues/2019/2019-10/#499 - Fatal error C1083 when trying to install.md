# #499 - Fatal error C1083 when trying to install [Open]

> Username: NemaJov  
> Created at: 2019-10-17 19:13:09 UTC  
> Updated at: 2021-05-29 18:22:41 UTC  
> Url: https://github.com/boostorg/build/issues/499  

I managed to build boost just fine on my computer using the following command:  
  
`b2.exe --with-iostreams --with-filesystem --with-regex --with-system --with-program_options --build-type=complete -sNO_ZLIB=0 -sZLIB_SOURCE=$(ZLIB_SOURCE) -sZLIB_BINARY=$(ZLIB_SOURCE)\Debug\zlibd.dll -sZLIB_INCLUDE=$(ZLIB_SOURCE)`  
  
However, when i try to do the same on another computer, I get a series of errors similar to this one:  
  
`libs\program_options\src\config_file.cpp(8): fatal error C1083: Cannot open include file: 'boost/program_options/config.hpp': No such file or directory`  
  
I have run bootstrap.bat successfully on both machines. I am 100% sure that both versions of boost are the same, since they are both cloned recursively from this repository. Is there an environment variable that has to be set in order for boost to find its own files, or is the issue somewhere else?

---

## Comment 1

> Username: grafikrobot  
> Created at: 2019-10-28 15:00:55 UTC  
> Url: https://github.com/boostorg/build/issues/499#issuecomment-546987487  

Please post output of running with `--debug-configuration` option.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2021-05-29 18:22:11 UTC  
> Url: https://github.com/boostorg/build/issues/499#issuecomment-850877360  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
