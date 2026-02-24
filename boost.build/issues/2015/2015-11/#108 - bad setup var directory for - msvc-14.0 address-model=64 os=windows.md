# #108 - bad setup var directory for : msvc-14.0 address-model=64 os=windows [Open]

> Username: Suryavarman  
> Created at: 2015-11-19 13:41:28 UTC  
> Updated at: 2015-11-19 13:41:28 UTC  
> Url: https://github.com/boostorg/build/issues/108  

For this cmd :  
X:BLABLA1\boost-build\build\b2.exe toolset=msvc-14.0 variant=release threading=multi link=shared address-model=64 warnings=on target-os=windows -d1 > X:BLABLA2\test\test_boost-build\test_helloworld\bin\msvc-14.0-64-release-64-multi.log  
  
it doesn't works :   
failed compile-c-c++ bin\msvc-14.0\release\address-model-64\threading-multi\hello.obj...  
  
Because of b2 call that :   
'"C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\vcvarsall.bat" amd64' >nul  
  
And it doesn't work if i try my self to see the output of this cmd:   
«The specified configuration type is missing.  The tools for the  
configuration might not be installed.»  
  
I use visual 2015 express and the right command is :   
call "C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\bin\x86_amd64\vcvarsx86_amd64.bat"
