# #462 - C++20 removed std::result_of [Closed]

> Username: 215020267  
> Created at: 2020-02-14 06:19:27 UTC  
> Updated at: 2020-08-26 18:22:05 UTC  
> Closed at: 2020-08-26 18:22:05 UTC  
> Url: https://github.com/boostorg/hana/issues/462  

**Description:**  
In /std:c++17 mode, this triggers a deprecation warning in recent versions of MSVC. In /std:c++latest mode, now that microsoft/STL#380 has been merged, this will trigger an error in VS 2019 16.6 Preview 2.  
  
**Reproduce steps:**  
1. git clone -b develop https://github.com/boostorg/hana  D:\Hana\src    
2. cd D:\Hana\src   
3. mkdir build_amd64  
4. cd D:\Hana\src\build_amd64  
5. cmake -G "Visual Studio 15 Win64" -DCMAKE_SYSTEM_VERSION=10.0.17134.0 -DBOOST_ROOT=F:\tools\boost_1_70_0\x64\boost -DBOOST_INCLUDEDIR=F:\tools\boost_1_70_0\x64 -DCMAKE_CXX_FLAGS="/EHsc /Zc:ternary /bigobj" -DBOOST_HANA_ENABLE_STRING_UDL=false .. 2>&1  
6. msbuild /m /p:Platform=x64 /p:Configuration=Release Boost.Hana.sln /t:Rebuild 2>&1  
  
**ErrorMessage:**  
F:\gitP\boostorg\hana\include\boost/hana/traits.hpp(197): error C2039: 'result_of': is not a member of 'std' [F:\gitP\boostorg\hana\build_amd64\example\example.monadic_fold_left.vcxproj]

---

## Comment 1

> Username: runi0s  
> Created at: 2020-02-23 08:55:00 UTC  
> Url: https://github.com/boostorg/hana/issues/462#issuecomment-590046775  

same for `std :: is_literal_type`.

---

## Comment 2

> Username: staffantj  
> Created at: 2020-05-28 20:03:03 UTC  
> Url: https://github.com/boostorg/hana/issues/462#issuecomment-635566148  

Microsoft has now released the 16.6 toolchain as their production version, so this is now breaking our production builds.
