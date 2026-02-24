# #512 - Hana failed to build with error C2065, error C2977, error C2913... on MSVC [Open]

> Username: fangzhouxia  
> Created at: 2022-12-09 02:34:57 UTC  
> Updated at: 2023-03-13 17:38:08 UTC  
> Url: https://github.com/boostorg/hana/issues/512  

**Describe**  
Hi all,  
Hana failed to build with error C2065, error C2977, error C2913... on MSVC on windows. Could you please help look at this issue or provide some workarounds? Thanks in advance.  
  
**Steps to reproduce**  
  
1.  open VS2019 x64 tools command  
2.  git clone https://github.com/boostorg/hana F:\gitP\boostorg\hana  
3. cd F:\gitP\boostorg\hana & mkdir build_amd64 & cd F:\gitP\boostorg\hana\build_amd64  
4.  cmake -G "Visual Studio 16 2019" -A x64 -DCMAKE_SYSTEM_VERSION=10.0.18362.0 -DBOOST_ROOT=F:\tools\boost_1_70_0\x64\boost -DBOOST_INCLUDEDIR=F:\tools\boost_1_70_0\x64 -DCMAKE_CXX_FLAGS="/EHsc /Zc:ternary /bigobj" -DBOOST_HANA_ENABLE_STRING_UDL=false ..   
5.  msbuild /m /p:Platform=x64 /p:Configuration=Release Boost.Hana.sln /t:Rebuild   
  
**log:**  
[build (4).log](https://github.com/boostorg/hana/files/10191290/build.4.log)  
F:\gitP\boostorg\hana\example\adapt_adt.cpp(38,1): error C2065: 'name': undeclared identifier [F:\gitP\boostorg\hana\build_amd64\example\example.adapt_adt.vcxproj]  
F:\gitP\boostorg\hana\example\adapt_adt.cpp(38,1): error C2977: 'boost::hana::accessors_impl': too many template arguments [F:\gitP\boostorg\hana\build_amd64\example\example.adapt_adt.vcxproj]  
F:\gitP\boostorg\hana\example\adapt_adt.cpp(41,1): error C2913: explicit specialization; 'boost::hana::accessors_impl' is not a specialization of a class template [F:\gitP\boostorg\hana\build_amd64\example\example.adapt_adt.vcxproj]  
F:\gitP\boostorg\hana\include\boost/hana/concept/struct.hpp(26,25): error C3203: 'accessors_impl': unspecialized class template can't be used as a template argument for template parameter 'Method', expected a real type [F:\gitP\boostorg\hana\build_amd64\example\example.adapt_adt.vcxproj]  
F:\gitP\boostorg\hana\include\boost/hana/accessors.hpp(33,20): error C2825: 'Accessors': must be a class or namespace when followed by '::' [F:\gitP\boostorg\hana\build_amd64\example\example.adapt_adt.vcxproj]  
    40>F:\gitP\boostorg\hana\include\boost/hana/accessors.hpp(33,31): error C2510: 'Accessors': left of '::' must be a class/struct/union [F:\gitP\boostorg\hana\build_amd64\example\example.adapt_adt.vcxproj]  
    40>F:\gitP\boostorg\hana\include\boost/hana/accessors.hpp(33,1): error C3889: call to object of class type 'boost::hana::apply_t': no matching call operator found   
  
**Versions and configuration**  
VS version: VS2019(16.11.20)  
Operating system: windows server 2019  
The commit is 944f717

---

## Comment 1

> Username: wilhelm-now  
> Created at: 2023-01-29 19:06:57 UTC  
> Url: https://github.com/boostorg/hana/issues/512#issuecomment-1407744998  

With Visual Studio 17 2022 17.4.4 I've made boost-hana from boost 1.81 dependent projects build with adding compile options [/Zc:__cplusplus](https://learn.microsoft.com/en-us/cpp/build/reference/zc-cplusplus) and [/Zc:preprocessor](https://learn.microsoft.com/en-us/cpp/build/reference/zc-preprocessor). This got rid of the accessors_impl related warnings.  
  
I still have some trouble building all of the git repo.

---

## Comment 2

> Username: ldionne  
> Created at: 2023-03-13 17:38:08 UTC  
> Url: https://github.com/boostorg/hana/issues/512#issuecomment-1466604834  

Can you try again now? I think that is likely fixed by https://github.com/boostorg/hana/commit/ff423df2c0e6bab8735ac407a8ac07bc023b254d.  
  
I don't have access to a Windows machine handy.
