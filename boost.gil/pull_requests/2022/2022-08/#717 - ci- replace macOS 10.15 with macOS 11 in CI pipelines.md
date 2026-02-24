# #717 ci: replace macOS 10.15 with macOS 11 in CI pipelines [Merged]

> Username: striezel  
> Created at: 2022-08-11 16:43:49 UTC  
> Updated at: 2025-08-03 18:11:53 UTC  
> Merged at: 2022-08-12 07:21:13 UTC  
> Closed at: 2022-08-12 07:21:13 UTC  
> Url: https://github.com/boostorg/gil/pull/717  

### Description  
  
GitHub started the deprecation process for macOS 10.15 runners on 2022-05-31, and macOS 10.15 will be completely unsupported by 2022-08-30 (if things proceed as planned). That is, in less than a month macOS builds would fail permanently, so the corresponding jobs are moved to macOS 11.  
  
### References  
  
This issue already hit us during the build for PR #716, because at that time there was a scheduled brownout for macOS 10.15 builds to raise awareness of the upcoming removal. It certainly worked. :D  
  
See <https://github.com/actions/runner-images/issues/5583> for more information on the removal of the macOS 10.15 images.  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: striezel  
> Created_at: 2022-08-11 17:01:22 UTC  
> Url: https://github.com/boostorg/gil/pull/717#issuecomment-1212252491  

On second thought, it might also be worth to consider the removal of Azure builds altogether.  
As things are now, AzP are used for builds with GCC 6 and GCC 8 on Linux and with Xcode 13(?) on macOS 11. However, GCC 6 and 8 are also covered by the GitHub Actions workflow, and macOS builds with Xcode are covered by GHA, too. So there seems to be no benefit to keeping the AzP stuff, as far as toolset coverage is concerned. Or am I missing something? So I could update this PR to remove the Azure stuff, if that is acceptable. Any thoughts on that?

---

## Comment 2

> Username: striezel  
> Created_at: 2022-08-11 17:17:02 UTC  
> Url: https://github.com/boostorg/gil/pull/717#issuecomment-1212267096  

Just updated this PR to only replace the macOS image on GHA. New macOS passed on GHA, but it failed on Azure.  
  
So I will probably add another PR that will remove Azure builds.

---

## Comment 3

> Username: striezel  
> Created_at: 2022-08-11 18:27:51 UTC  
> Updated_at: 2022-08-11 18:28:16 UTC  
> Url: https://github.com/boostorg/gil/pull/717#issuecomment-1212347074  

Even the old macOS version failed on Azure: https://dev.azure.com/boostorg/gil/_build/results?buildId=2042&view=logs&jobId=428b2f5c-d4ea-5628-f40b-db0b1f2cd10b&j=428b2f5c-d4ea-5628-f40b-db0b1f2cd10b&t=a1e26ec1-1cd6-52b3-5716-ab36bddef14c  
  
```  
2022-08-11T17:19:23.8573400Z -- Conan executing: /usr/local/bin/conan install /Users/runner/work/1/s/boost-root/libs/gil/conanfile.txt -s build_type=Release -s compiler=apple-clang -s compiler.version=12.0 -s compiler.libcxx=libc++ -g=cmake --build=missing  
2022-08-11T17:19:24.4057570Z Auto detecting your dev setup to initialize the default profile (/Users/runner/.conan/profiles/default)  
2022-08-11T17:19:24.4068430Z CC and CXX: None, /Applications/Xcode_12.4.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++   
2022-08-11T17:19:24.4070330Z No compiler was detected (one may not be needed)  
2022-08-11T17:19:24.4072520Z ERROR: Not able to automatically detect '/Applications/Xcode_12.4.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++' version  
2022-08-11T17:19:24.4074910Z Default settings  
2022-08-11T17:19:24.4075950Z 	os=Macos  
2022-08-11T17:19:24.4076920Z 	os_build=Macos  
2022-08-11T17:19:24.4077890Z 	arch=x86_64  
2022-08-11T17:19:24.4078800Z 	arch_build=x86_64  
2022-08-11T17:19:24.4080060Z 	build_type=Release  
2022-08-11T17:19:24.4080990Z *** You can change them in /Users/runner/.conan/profiles/default ***  
2022-08-11T17:19:24.4082620Z *** Or override with -s compiler='other' -s ...s***  
```  
  
macOS 11 failed with a similar error, too: https://dev.azure.com/boostorg/gil/_build/results?buildId=2041&view=logs&j=dbffb207-18be-561b-b0ae-80a806713608&t=c389dadf-49bf-5055-b7a5-f2217ec3ecf5

---

## Comment 4

> Username: mloskot  
> Created_at: 2022-08-11 19:42:19 UTC  
> Url: https://github.com/boostorg/gil/pull/717#issuecomment-1212415021  

>  it might also be worth to consider the removal of Azure builds altogether.  
  
Yes, I think it's desirable to clean this up.  
Originally (or AFAIR), the AzP builds were added to build using our contributor-oriented CMake configuration.  
  
However, as we have planned to clean the CI up, indeed, we should strive for usable bare minimum first.

---

## Comment 5

> Username: striezel  
> Created_at: 2022-08-11 21:42:45 UTC  
> Url: https://github.com/boostorg/gil/pull/717#issuecomment-1212525656  

> Yes, I think it's desirable to clean this up.  
  
Alright. I just pushed another commit that removes the Azure builds.

---

## Review 6 [Approved]

> Username: mloskot  
> Created_at: 2022-08-12 07:20:35 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/717#pullrequestreview-1070689923  

Thank you

---
