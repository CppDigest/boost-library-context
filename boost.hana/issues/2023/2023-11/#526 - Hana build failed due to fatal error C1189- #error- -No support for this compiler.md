# #526 - Hana build failed due to fatal error C1189: #error:  "No support for this compiler." [Open]

> Username: Zhaojun-Liu  
> Created at: 2023-11-29 10:02:59 UTC  
> Updated at: 2023-11-29 10:06:46 UTC  
> Url: https://github.com/boostorg/hana/issues/526  

Hi:  
Recently I update the Hana sha([e820551](https://github.com/boostorg/hana/commit/e820551959996bc031e72622fd731e9e7acd95b0)) to build with msvc, it failed due to `fatal error C1189: #error:  "No support for this compiler."` Could you please take a look? Thanks.  
  
Repro steps:  
1. open VS2019 x64 tools command  
2. git clone https://github.com/boostorg/hana F:\gitP\boostorg\hana  
3. mkdir F:\gitP\boostorg\hana\build_amd64 & cd F:\gitP\boostorg\hana\build_amd64  
4. cmake -G "Visual Studio 17 2022" -A x64 -DCMAKE_SYSTEM_VERSION=10.0.22621.0 -DBOOST_ROOT=F:\tools\boost_1_70_0\x64\boost -DBOOST_INCLUDEDIR=F:\tools\boost_1_70_0\x64 -DCMAKE_CXX_FLAGS="/EHsc /Zc:ternary /bigobj" -DBOOST_HANA_ENABLE_STRING_UDL=false ..   
5. msbuild /m /p:Platform=x64 /p:Configuration=Release Boost.Hana.sln /t:Rebuild  
  
Build results:  
`F:\gitP\boostorg\hana\include\boost/hana/experimental/type_name.hpp(41,1): fatal  error C1189: #error:  "No support for this compiler." [F:\gitP\boostorg\hana\build_amd64\test\test.experimental.type_name.vcxproj]`  
  
Detailed log:  
[build.log](https://github.com/boostorg/hana/files/13498957/build.log)  
  
Note: When I use commit d3634a3, there was no error.  
  
OS: Windows Server 2022 Datacenter 21H2  
VS: VS17.7.6
