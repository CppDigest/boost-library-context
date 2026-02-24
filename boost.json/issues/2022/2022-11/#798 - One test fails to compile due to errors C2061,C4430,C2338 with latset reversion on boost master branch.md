# #798 - One test fails to compile due to errors C2061,C4430,C2338 with latset reversion on boost master branch [Closed]

> Username: nnfdnkns  
> Created at: 2022-11-03 10:11:02 UTC  
> Updated at: 2022-11-11 20:16:28 UTC  
> Closed at: 2022-11-11 20:16:28 UTC  
> Url: https://github.com/boostorg/json/issues/798  

**Environment:**  
VS2019+Windows Server 2019  
  
**Issue description:**  
Found \boost\libs\json build fails to compile due to errors C2061,C4430,C2338 in MSVC, this issue can be reproduced on https://github.com/boostorg/boost/commit/97f075e. Could you please take a look?  
  
**Reproduce steps:**  
1.git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git F:\gitP\boostorg\boost  
2.open a VS 2019 x64 command prompt and browse to F:\gitP\boostorg\boost  
3..\bootstrap  
4..\b2 headers variant=release --build-dir=..\out\amd64rel address-model=64  
5..\b2 variant=release --build-dir=..\out\amd64rel address-model=64  
6..\b2 -j16 variant=release --build-dir=F:\gitP\boostorg\boost\out\amd64rel libs\json\test address-model=64 2>&1  
  
**ErrorMessage:**  
libs\json\test\conversion.cpp(115): error C2061: syntax error: identifier 'e'  
libs\json\test\conversion.cpp(115): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
libs\json\test\conversion.cpp(191): error C2338: static_assert failed: 'is_described_enum<described_enum>::value'  
...failed compile-c-c++ F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\json\test\conversion.test\msvc-14.2\release\threading-multi\conversion.obj...  
  
**log**  
[test.log.63.txt](https://github.com/boostorg/json/files/9927312/test.log.63.txt)

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-11-05 16:06:51 UTC  
> Url: https://github.com/boostorg/json/issues/798#issuecomment-1304574221  

This was fixed in d88c48c892390ae437214b843f73e81d7f1d37cc. And should be now in the master branch. Can you check if everything works now?

---

## Comment 2

> Username: grisumbras  
> Created at: 2022-11-11 20:16:28 UTC  
> Url: https://github.com/boostorg/json/issues/798#issuecomment-1312159445  

Beta reportedly works, so I'm closing this
