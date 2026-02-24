# #735 - Building Boost 1.77 using Visual Studio 2022 preview (working solution) [Closed]

> Username: larsgregersen  
> Created at: 2021-09-01 19:49:42 UTC  
> Updated at: 2022-02-21 12:47:24 UTC  
> Closed at: 2022-02-03 18:49:02 UTC  
> Url: https://github.com/boostorg/build/issues/735  

Building Boost 1.77 using Visual Studio 2022 fails when using Boost files from the repository as is.  
  
I have made building work with Boost 1.77, but it requires a few changes to the Boost files in order to get everything working.  
  
  
In boost_1_77_0\bootstrap.bat:  
 change  
    call .\build.bat  
 to  
    call .\build.bat msvc  
  
Add this line after the similar line that says something with vc142:  
IF "%1"=="vc143" SET TOOLSET=msvc : 14.3  
  
  
In boost_1_77_0\boost\config\auto_link.hpp:  
 Find this line:  
#  elif defined(BOOST_MSVC)  
 and edit the file such that it reads:  
#  elif defined(BOOST_MSVC) && (BOOST_MSVC < 1930)  
#    define BOOST_LIB_TOOLSET "vc142"  
#  elif defined(BOOST_MSVC)   
#    define BOOST_LIB_TOOLSET "vc143"  
  
  
In boost_1_77_0\tools\build\src\tools\msvc.jam:  
 Find this line:  
 if [ MATCH "(MSVC\\\\14.2)" : $(command) ]  
 and edit the file such that it reads:  
 if [ MATCH "(MSVC\\\\14.3)" : $(command) ]  
 {  
   version = 14.3 ;  
 }  
 else if [ MATCH "(MSVC\\\\14.2)" : $(command) ]  
  
 replace this line:  
 if $(version) in 14.1 14.2 default && $(root) && [ path.exists $(vswhere) ]  
 with this:  
 if $(version) in 14.1 14.2 14.3 default && $(root) && [ path.exists $(vswhere) ]  
  
 replace this line:  
 if $(version) = 14.2 || $(version) = "default"  
 with this:  
             if $(version) = 14.2 || $(version) = 14.3 || $(version) = "default"  
  
 replace this line:  
 .known-versions = 14.2 14.1 14.0 12.0 11.0 10.0 10.0express 9.0 9.0express 8.0 8.0express 7.1  
 with  
 .known-versions = 14.3 14.2 14.1 14.0 12.0 11.0 10.0 10.0express 9.0 9.0express 8.0 8.0express 7.1  
  
Copy  
c:\Program Files\Microsoft Visual Studio\2022\Preview\VC\Auxiliary\Build\vcvarsall.bat  
to  
C:\Program Files\Microsoft Visual Studio\2022\Preview\VC\Tools\MSVC\14.30.30423\bin\Hostx64\vcvarsall.bat  
[I couldn't find where this path is configured so I had to perform a manual copy]  
  
When building do this:  
- Start the x64 Native Tools Command Prompt for VS 2022  
- cd to the boost_1_77_0 directory  
- run bootstrap.bat  
- run b2 address-model=64

---

## Comment 1

> Username: ohhmm  
> Created at: 2021-09-16 11:18:07 UTC  
> Url: https://github.com/boostorg/build/issues/735#issuecomment-920811762  

[bootstrap.zip](https://github.com/boostorg/build/files/7177223/bootstrap.zip)

---

## Comment 2

> Username: mloskot  
> Created at: 2021-10-14 13:44:58 UTC  
> Url: https://github.com/boostorg/build/issues/735#issuecomment-943373105  

@larsgregersen   
> Add this line after the similar line that says something with vc142:  
> `IF "%1"=="vc143" SET TOOLSET=msvc : 14.3`  
  
Here is relevant PR https://github.com/boostorg/boost/pull/567 which was missing from the recent @ohhmm 's PRs.  
  
p.s. @larsgregersen Please, try some [backticks tricks](https://docs.github.com/en/github/writing-on-github/working-with-advanced-formatting/creating-and-highlighting-code-blocks) next time, thanks.

---

## Comment 3

> Username: mloskot  
> Created at: 2021-10-14 13:50:35 UTC  
> Updated at: 2021-10-14 13:52:21 UTC  
> Url: https://github.com/boostorg/build/issues/735#issuecomment-943377899  

### Building Boost 1.77 from source archive using Visual Studio 2022  
  
Here is quick list of **three patching** steps to enable building of the already released Boost 1.77:  
  
1.  Unpack `boost_1_77_0.7z` and `cd boost_1_77_0`  
2. `curl -O https://raw.githubusercontent.com/boostorg/boost/bc988929373dfc69e8f19d6d5932fe321f44617b/bootstrap.bat`  
3. `pushd boost/config` and `curl -O https://raw.githubusercontent.com/boostorg/config/develop/include/boost/config/auto_link.hpp` then `popd`  
4. `pushd tools` and `rm -rf build` and `git clone --branch master https://github.com/boostorg/build.git build` then `popd`  
  
It's ready to go!  
  
-----  
  
The patching delivers the following changes required by VS 2022:  
1. https://github.com/boostorg/boost/pull/567  
2. https://github.com/boostorg/config/pull/405  
3. https://github.com/bfgroup/b2/pull/89 which has landed in Boost in https://github.com/boostorg/build/commit/aaf14325ec8e3ca86be9afaa501bca68fcfea8b3

---

## Comment 4

> Username: LeeRuns  
> Created at: 2022-02-03 18:21:28 UTC  
> Url: https://github.com/boostorg/build/issues/735#issuecomment-1029272122  

Has this still not been merged into the 1.77 version?

---

## Comment 5

> Username: grafikrobot  
> Created at: 2022-02-03 18:49:02 UTC  
> Url: https://github.com/boostorg/build/issues/735#issuecomment-1029294928  

Boost 1.78 includes B2 4.7.2 <https://www.boost.org/doc/libs/1_78_0/tools/build/doc/html/#_version_4_7_2>. Which includes support for building with msvc 14.3, aka cl 17.x. Can't speak for any other Boost library changes.

---

## Comment 6

> Username: karan00713  
> Created at: 2022-02-14 09:23:02 UTC  
> Updated at: 2022-02-14 09:24:47 UTC  
> Url: https://github.com/boostorg/build/issues/735#issuecomment-1038848004  

Hi @grafikrobot   
I've tried Boost 1.78, but its gives out some errors, can you please take a look at this !  
  
**error: Name clash for '<pC:\Users\xxxx\xxxx\boost_1_78_0\stage\lib\cmake\boost_fiber-1.78.0>libboost_fiber-variant-vc143-mt-gd-1_78-static.cmake'  
error:  
error: Tried to build the target twice, with property sets having  
error: these incompatible properties:  
error:  
error:     -  address-model>32  
error:     -  address-model>64  
error:  
error: Please make sure to have consistent requirements for these  
error: properties everywhere in your project, especially for install  
error: targets.**
