# #72 - Ten tests from boost libraries failed to run after boost submodule variant updated from 7ecf72 to 0e122c [Closed]

> Username: spacelg  
> Created at: 2019-05-05 09:10:33 UTC  
> Updated at: 2019-05-09 07:00:11 UTC  
> Closed at: 2019-05-09 07:00:10 UTC  
> Url: https://github.com/boostorg/variant/issues/72  

Environment:  
VS 2017 + Windows Server 2016  
  
Issue description:  
Ten tests from boost libraries failed to run after boost submodule variant updated from 7ecf72 to 0e122c [boost/variant@1446f16](https://github.com/boostorg/boost/commit/1446f169b421a5826d29981d0abd644979519b28). Could you please take a look?  
  
Reproduce steps:  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x64 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\variant\test  
  
[log_x64_test_119.log](https://github.com/boostorg/variant/files/3145319/log_x64_test_119.log)  
  
ErrorMessage:  
D:\Boost\src\boost\variant\detail\forced_return.hpp(43) : error C4716: <func:#1252 "??$forced_return@ABVtype_info@@@variant@detail@boost@@YAABVtype_info@@XZ"> 'boost::detail::variant::forced_return<type_info const &>': must return a value  
D:\Boost\src\boost\variant\detail\forced_return.hpp(43) : error C4716: <func:#1277 "??$forced_return@H@variant@detail@boost@@YAHXZ"> 'boost::detail::variant::forced_return<int>': must return a value

---

## Comment 1

> Username: apolukhin  
> Created at: 2019-05-05 11:14:50 UTC  
> Url: https://github.com/boostorg/variant/issues/72#issuecomment-489416428  

Boost.Config needs develop to master merge to resolve the issue. In particular commit https://github.com/boostorg/config/commit/6231009e97daf0c501aa6d3a67688dcdfced2be5 should be merged to master.

---

## Comment 2

> Username: apolukhin  
> Created at: 2019-05-05 11:16:40 UTC  
> Url: https://github.com/boostorg/variant/issues/72#issuecomment-489416609  

@jzmaddock , could you please merge the https://github.com/boostorg/config/commit/6231009e97daf0c501aa6d3a67688dcdfced2be5 into the master of Boost.Config?

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-05-05 17:33:45 UTC  
> Url: https://github.com/boostorg/variant/issues/72#issuecomment-489446867  

Done.  
  
However, shouldn't use of BOOST_UNREACHABLE_RETURN be a compiler-hint / warning suppression mechanism and not be dependent upon compiler magic which may or may not be present?

---

## Comment 4

> Username: apolukhin  
> Created at: 2019-05-09 07:00:10 UTC  
> Url: https://github.com/boostorg/variant/issues/72#issuecomment-490769291  

In boost::variant it is used as a warning suppression mechanism:  
https://github.com/boostorg/variant/blob/15a846fba0d52e8cb6b8e0459048df5b73c86706/include/boost/variant/detail/forced_return.hpp#L33-L43  
  
Why it was causing hard failures without https://github.com/boostorg/config/commit/6231009e97daf0c501aa6d3a67688dcdfced2be5 is a good question. @jzmaddock probably we're misusing the macro, but there are usage limitations in the docs.
