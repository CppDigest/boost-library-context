# #11 - test_codecvt fails on Windows, VS2015, boost v1.62 [Closed]

> Username: mmatrosov  
> Created at: 2016-11-12 16:35:25 UTC  
> Updated at: 2017-06-10 14:30:01 UTC  
> Closed at: 2017-06-10 14:30:01 UTC  
> Url: https://github.com/boostorg/nowide/issues/11  

Environment:   
* Windows 7 x64 with latest updates  
* Visual Studio 2015 Update 3  
  
Steps to reproduce:  
1. Download boost pre-built Windows binaries for Visual Studio 2015 32-bit and install to `<boost_dir>`  
1. Run `<boost_dir>\tools\build\bootstrap.bat`  
1. Download latest version of nowide lib  
1. Copy contents of the archive to `<boost_dir>\libs\nowide`  
1. Run `<boost_dir>\tools\build\b2.exe` in `<boost_dir>\libs\nowide\build\`  
1. Run `<boost_dir>\tools\build\b2.exe` in `<boost_dir>\libs\nowide\test\`  
1. Run `<boost_dir>\bin.v2\libs\nowide\test\test_codecvt.test\msvc-14.0\debug\test_codecvt.exe`  
  
Test output:  
> Conversions  
> Wlen=1 Nlen=1  
> Failed : Error count == to_next - to in test_codecvt.cpp:68 test_codecvt_in_n_m  
  
All other tests passed. Except for `test_system_*` which are supposed to be run with some arguments, not sure how to use it.  
  
Same behavior for release configuration.

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2017-06-10 14:30:01 UTC  
> Url: https://github.com/boostorg/nowide/issues/11#issuecomment-307568514  

Fixed in 24ef5223bb688e91369d69bccd22b522efd5c5a3
