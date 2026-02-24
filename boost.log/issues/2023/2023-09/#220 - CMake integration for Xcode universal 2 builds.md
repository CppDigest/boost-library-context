# #220 - CMake integration for Xcode universal 2 builds [Closed]

> Username: EikeAtOT  
> Created at: 2023-09-28 12:53:22 UTC  
> Updated at: 2023-09-28 13:29:50 UTC  
> Closed at: 2023-09-28 13:29:50 UTC  
> Url: https://github.com/boostorg/log/issues/220  

The current CMake implementation does not work on Xcode universal 2 builds (x86_64 and arm64) due to the way compiler flags are discovered and set. A universal build (where CMAKE_OSX_ARCHITECTURES = "x86_64;arm64") will fail when trying to compile dump_ssse3.cpp and dump_avx2.cpp

---

## Comment 1

> Username: Lastique  
> Created at: 2023-09-28 13:29:50 UTC  
> Url: https://github.com/boostorg/log/issues/220#issuecomment-1739184429  

Sorry, I have no experience with Xcode or Mac OS development and have no idea how universal builds work. You can consider this configuration untested and unsupported. PRs are welcome, although, again, I won't be able to test or maintain that configuration.
