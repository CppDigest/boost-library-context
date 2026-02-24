# #137 - mingw64: error: No best alternative for libs/context/build/asm_sources [Closed]

> Username: jschueller  
> Created at: 2020-04-24 09:28:37 UTC  
> Updated at: 2021-07-13 19:05:35 UTC  
> Closed at: 2021-07-13 19:05:35 UTC  
> Url: https://github.com/boostorg/context/issues/137  

when I use mingw (gcc 9.3) from Linux context throw this warning at configure time:  
error: No best alternative for libs/context/build/asm_sources  
and fails to build later with some undefined references  
  
my user-config.jam looks like this:  
using gcc : mingw64 : x86_64-w64-mingw32-g++  
  
a workaround is to patch in build/Jamfile.v2 default_binary_format and default_abi to return pe and ms  
  
I tried latest 1.73.0rc1

---

## Comment 1

> Username: olk  
> Created at: 2020-04-24 09:36:06 UTC  
> Url: https://github.com/boostorg/context/issues/137#issuecomment-618909748  

Error message means that boost.build can not determine which asm file has to be choosen.  
please provide a patch

---

## Comment 2

> Username: jschueller  
> Created at: 2020-04-24 09:38:00 UTC  
> Url: https://github.com/boostorg/context/issues/137#issuecomment-618910580  

yes, the root of all this is that the abi and binary format are incorrect.

---

## Comment 3

> Username: olk  
> Created at: 2021-07-13 19:05:35 UTC  
> Url: https://github.com/boostorg/context/issues/137#issuecomment-879329552  

closing because no progress
