# #497 Update BOOST_COMPILER_VERSION for msvc 19.4x (VS 2022 17.10) [Merged]

> Username: avjts  
> Created at: 2024-05-28 11:58:42 UTC  
> Updated at: 2025-12-15 13:12:17 UTC  
> Merged at: 2025-12-15 13:12:17 UTC  
> Closed at: 2025-12-15 13:12:17 UTC  
> Url: https://github.com/boostorg/config/pull/497  

Fixes boostorg/config#496  
  
According to the anouncement https://devblogs.microsoft.com/cppblog/msvc-toolset-minor-version-number-14-40-in-vs-2022-v17-10/ and the 17.10 VS release   
toolset name "v143" is retained  
toolset version is extended by another decade  
and we can assume the _MSC_VER follows this pattern

---
