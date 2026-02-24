# #398 Fix build with Embarcadero C++ Builder ("borland" toolset) [Merged]

> Username: tanzislam  
> Created at: 2019-02-23 05:33:56 UTC  
> Updated at: 2021-10-02 21:19:16 UTC  
> Merged at: 2019-02-27 01:32:53 UTC  
> Closed at: 2019-02-27 01:32:53 UTC  
> Url: https://github.com/boostorg/build/pull/398  

Fix several problems with building with the `borland` toolset on a Embarcadero C++ Builder 10.3.1 ([Community Edition](https://www.embarcadero.com/products/cbuilder/starter)) installation:  
  
 - `bootstrap.bat` -> `src/engine/build.bat` bypasses loading `src/engine/guess_toolset.bat` if an override toolset is given. This later leads to the call to `Test_Path` failing. (Issue #382)  
 - C++Builder toolchain path is intolerant of spaces, e.g. `C:\Program Files\Embarcadero\…`  
 - Old Borland (`bcc32.exe`) preprocessor doesn't accept `#if SYMBOL_NAME`, but insists on `#if defined(SYMBOL_NAME)` or `#ifdef SYMBOL_NAME`. Work around this by providing `-Nd` to auto-define `SYMBOL_NAME` to `1`.  
  
With these three fixes, running `bootstrap.bat borland` in the super-project directory should successfully produce a working `b2.exe`.  
  
NOTE: To actually build some Boost libraries with the `borland` toolset, we also need a `user-config.jam` file to force the use of `bcc32c.exe` for modern C++ support:  
  
```  
using borland : 7.40 : bcc32c.exe -DBOOST_STRICT_CONFIG -DBOOST_ASSERT_CONFIG ;  
```  
  
(I've [managed to build](https://github.com/tanzislam/cryptopals/wiki/Importing-into-Embarcadero-C┼┼Builder-Starter-10.2#linking-in-a-boost-library) Boost.System, Boost.DateTime and Boost.Regex in this manner.)

---

## Comment 1

> Username: tanzislam  
> Created_at: 2019-02-23 08:18:49 UTC  
> Updated_at: 2019-02-23 22:11:29 UTC  
> Url: https://github.com/boostorg/build/pull/398#issuecomment-466628355  

Hmm … not sure what went wrong with the AppVeyor build. I could not reproduce the issue on my side with the following in a plain `cmd.exe` window:  
  
```  
git clone https://github.com/tanzislam/build.git  
cd build  
git checkout origin/fix-borland-build  
set PATH=C:\Windows\system32;C:\Windows;C:\Windows\system32\Wbem  
cd src\engine  
build.bat  
```  
  
(I have VS2017 Community v15.9.7 and Git for Windows v2.19.2.windows.1 installed in my Windows 7 x64 SP1 laptop.)  
  
**UPDATE:** OK fixed it. This was a latent bug introduced in https://github.com/boostorg/build/commit/00c23580afdd519fa716ee32d6dba500e6b1e991#diff-dd107c2c786291c148ab9be98cc7b2ef where the `Guess_Toolset` logic bled into `Guess_Yacc`, and it somehow got exposed by my 9339693c0af change.

---
