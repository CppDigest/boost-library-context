# #176 - A hash error was displayed when downloading using vcpkg [Open]

> Username: PangYanze  
> Created at: 2024-06-27 13:18:28 UTC  
> Updated at: 2024-08-15 23:35:48 UTC  
> Url: https://github.com/boostorg/core/issues/176  

./vcpkg install boost-core:x64-windows  
Computing installation plan...  
The following packages will be built and installed:  
    boost-core:x64-windows@1.85.0  
Detecting compiler hash for triplet x64-windows...  
Compiler found: C:/Program Files/Microsoft Visual Studio/2022/Community/VC/Tools/MSVC/14.40.33807/bin/Hostx64/x64/cl.exeRestored 0 package(s) from C:\Users\13006\AppData\Local\vcpkg\archives in 215 us. Use --debug to see more details.  
Installing 1/1 boost-core:x64-windows@1.85.0...  
Building boost-core:x64-windows@1.85.0...  
-- Downloading https://github.com/boostorg/core/archive/boost-1.85.0.tar.gz -> boostorg-core-boost-1-3a7be75e.85.0.tar.gz...  
[DEBUG] To include the environment variables in debug output, pass --debug-env  
[DEBUG] Trying to load bundleconfig from F:\cppProject\vcpkg-2024.05.24\vcpkg-bundle.json  
[DEBUG] Failed to open: F:\cppProject\vcpkg-2024.05.24\vcpkg-bundle.json  
[DEBUG] Bundle config: readonly=false, usegitregistry=false, embeddedsha=nullopt, deployment=Git, vsversion=nullopt  
[DEBUG] Metrics enabled.  
[DEBUG] Feature flag 'binarycaching' unset  
[DEBUG] Feature flag 'compilertracking' unset  
[DEBUG] Feature flag 'registries' unset  
[DEBUG] Feature flag 'versions' unset  
[DEBUG] Feature flag 'dependencygraph' unset  
Downloading https://github.com/boostorg/core/archive/boost-1.85.0.tar.gz  
[DEBUG] Trying to hash F:\cppProject\vcpkg-2024.05.24\downloads\boostorg-core-boost-1-3a7be75e.85.0.tar.gz.16176.part  
[DEBUG] F:\cppProject\vcpkg-2024.05.24\downloads\boostorg-core-boost-1-3a7be75e.85.0.tar.gz.16176.part has hash a1e774c98b537dd42018742af68f5889af1afd4e6c9809399987124e56a3b9ae2f20e8173170bc6fc0ad76fb95e2da7b9d385a4037e9ee12011fc02bf8f7e009  
error: Failed to download from mirror set  
error: File does not have the expected hash:  
url: https://github.com/boostorg/core/archive/boost-1.85.0.tar.gz  
File: F:\cppProject\vcpkg-2024.05.24\downloads\boostorg-core-boost-1-3a7be75e.85.0.tar.gz.16176.part  
Expected hash: 3a7be75e52f5c20830fccb9e7391a1e4556ebb072e6324df95b1ba38bed46e24f4c9f27a62a1099eddc90f2ac1ede083f0c850e2dc27fd42375d028516f675b3  
Actual hash: a1e774c98b537dd42018742af68f5889af1afd4e6c9809399987124e56a3b9ae2f20e8173170bc6fc0ad76fb95e2da7b9d385a4037e9ee12011fc02bf8f7e009  
[DEBUG] D:\a\_work\1\s\src\vcpkg\base\downloads.cpp(997):  
[DEBUG] Time in subprocesses: 0us  
[DEBUG] Time in parsing JSON: 7us  
[DEBUG] Time in JSON reader: 0us  
[DEBUG] Time in filesystem: 1443us  
[DEBUG] Time in loading ports: 0us  
[DEBUG] Exiting after 4.3 s (4318403us)  
  
CMake Error at scripts/cmake/vcpkg_download_distfile.cmake:32 (message):  
  
      Failed to download file with error: 1  
      If you are using a proxy, please check your proxy setting. Possible causes are:  
  
      1. You are actually using an HTTP proxy, but setting HTTPS_PROXY variable  
         to `https://address:port`. This is not correct, because `https://` prefix  
         claims the proxy is an HTTPS proxy, while your proxy (v2ray, shadowsocksr  
         , etc..) is an HTTP proxy. Try setting `http://address:port` to both  
         HTTP_PROXY and HTTPS_PROXY instead.  
  
      2. If you are using Windows, vcpkg will automatically use your Windows IE Proxy Settings  
         set by your proxy software. See https://github.com/microsoft/vcpkg-tool/pull/77  
         The value set by your proxy might be wrong, or have same `https://` prefix issue.  
  
      3. Your proxy's remote server is out of service.  
  
      If you've tried directly download the link, and believe this is not a temporary  
      download server failure, please submit an issue at https://github.com/Microsoft/vcpkg/issues  
      to report this upstream download server failure.  
  
  
Call Stack (most recent call first):  
  scripts/cmake/vcpkg_download_distfile.cmake:270 (z_vcpkg_download_distfile_show_proxy_and_fail)  
  scripts/cmake/vcpkg_from_github.cmake:106 (vcpkg_download_distfile)  
  ports/boost-core/portfile.cmake:3 (vcpkg_from_github)  
  scripts/ports.cmake:175 (include)  
  
  
error: building boost-core:x64-windows failed with: BUILD_FAILED  
Elapsed time to handle boost-core:x64-windows: 4.4 s  
Please ensure you're using the latest port files with `git pull` and `vcpkg update`.  
Then check for known issues at:  
  https://github.com/microsoft/vcpkg/issues?q=is%3Aissue+is%3Aopen+in%3Atitle+boost-core  
You can submit a new issue at:  
  https://github.com/microsoft/vcpkg/issues/new?title=[boost-core]+Build+error+on+x64-windows&body=Copy+issue+body+from+F%3A%2FcppProject%2Fvcpkg-2024.05.24%2Finstalled%2Fvcpkg%2Fissue_body.md

---

## Comment 1

> Username: pdimov  
> Created at: 2024-06-27 13:21:47 UTC  
> Url: https://github.com/boostorg/core/issues/176#issuecomment-2194681086  

You need to update your vcpkg installation, as the error message suggests:  
  
> Please ensure you're using the latest port files with `git pull` and `vcpkg update`.

---

## Comment 2

> Username: Lastique  
> Created at: 2024-06-27 14:23:49 UTC  
> Url: https://github.com/boostorg/core/issues/176#issuecomment-2194842465  

Also, vcpkg issues should be reported to Microsoft.

---

## Comment 3

> Username: davidebeatrici  
> Created at: 2024-08-15 23:35:46 UTC  
> Url: https://github.com/boostorg/core/issues/176#issuecomment-2292457746  

I can confirm the issue and @pdimov is right, the hashes were updated in microsoft/vcpkg#38979.  
  
As for the hash change, it's explained in microsoft/vcpkg#38974.
