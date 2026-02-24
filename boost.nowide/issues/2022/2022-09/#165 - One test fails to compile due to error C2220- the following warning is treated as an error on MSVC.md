# #165 - One test fails to compile due to error C2220: the following warning is treated as an error on MSVC [Closed]

> Username: nnfdnkns  
> Created at: 2022-09-27 02:51:26 UTC  
> Updated at: 2022-11-05 11:56:36 UTC  
> Closed at: 2022-09-27 15:39:12 UTC  
> Url: https://github.com/boostorg/nowide/issues/165  

**Issue description:**  
  
Found boost\libs\nowide build error C2220 in MSVC, this issue can be reproduced on https://github.com/boostorg/mp11/commit/c92d2d0b6fa9ae77094416f8142a8128c0424941. Could you please take a look?  
  
**Reproduce steps:**  
1.git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git F:\gitP\boostorg\boost  
2.open a VS 2019 x64 command prompt and browse to F:\gitP\boostorg\boost  
3..\bootstrap  
4..\b2 headers variant=release --build-dir=..\out\amd64rel address-model=64  
5..\b2 variant=release --build-dir=..\out\amd64rel address-model=64  
6..\b2 -j16 variant=release --build-dir=F:\gitP\boostorg\boost\out\amd64rel libs\nowide\test address-model=64  
  
**ErrorMessage:**  
 error C2220: the following warning is treated as an error  
...failed compile-c-c++ F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\nowide\test\test_traits.test\msvc-14.2\release\threading-multi\test_traits.obj...  
  
 **log:**  
[test.log](https://github.com/boostorg/nowide/files/9651091/test.log)  
[test.log.80.txt](https://github.com/boostorg/nowide/files/9651089/test.log.80.txt)

---

## Comment 1

> Username: Flamefire  
> Created at: 2022-09-27 10:37:50 UTC  
> Url: https://github.com/boostorg/nowide/issues/165#issuecomment-1259312935  

Thanks for the report, I opened a PR fixing this.  
  
The commit link you posted points to mp11 which is unrelated. I guess a C&P mistake but would be good to correct if possible.  
  
Also posting the actual warning which this relates to would have made the issue much more descriptive, i.e. the full:  
  
```  
libs\nowide\test\test_traits.cpp(31): error C2220: the following warning is treated as an error  
libs\nowide\test\test_traits.cpp(31): warning C4005: '_SILENCE_EXPERIMENTAL_FILESYSTEM_DEPRECATION_WARNING': macro redefinition  
libs\nowide\test\test_traits.cpp(31): note: '_SILENCE_EXPERIMENTAL_FILESYSTEM_DEPRECATION_WARNING' previously declared on the command line  
```  
  
Anyway thanks for attaching the logs which helped finding this!  
  
For the record: This actually looks like a compiler bug as the compiler invocation (from the log) is:  
  
>  cl /Zm800 -nologo "libs\nowide\test\test_traits.cpp" -c -Fo"F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\nowide\test\test_traits.test\msvc-14.2\release\threading-multi\test_traits.obj"     -TP /wd4675 /EHs /GR /Zc:throwingNew /O2 /Ob2 /W4 /WX /MD /Zc:forScope /Zc:wchar_t /Zc:inline /Gw /favor:blend -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_NOWIDE_DYN_LINK=1 -DBOOST_NOWIDE_TEST_BFS_PATH -DNDEBUG "-I." "-Ilibs\nowide\test"   
  
I.e. the define isn't on the command line as reported by the warning.

---

## Comment 2

> Username: nnfdnkns  
> Created at: 2022-11-05 11:56:36 UTC  
> Url: https://github.com/boostorg/nowide/issues/165#issuecomment-1304507052  

thanks!
