# #579 - Useless extra symlinks installed for shared libs [Open]

> Username: jwakely  
> Created at: 2020-04-24 23:22:10 UTC  
> Updated at: 2021-05-29 17:22:47 UTC  
> Url: https://github.com/boostorg/build/issues/579  

At some point after boost 1.69.0 every shared library also acquired useless symlinks on linux:  
  
````  
libboost_system.so.1.73.0  
libboost_system.so.1.73  
libboost_system.so.1  
libboost_system.so  
````  
The first file is needed (it's the actual shared library) and the last one is needed (it's the symlink that is used when you link with `-lboost_system`) but the second and third are useless.  
  
Creating those extra symlinks is done by `installed-shared-lib-generator` in `tools/build/src/tools/stage.jam` and is controlled by `<install-no-version-symlinks>on`. That used to be set in `boostcpp.jam` but that was removed in  
https://github.com/boostorg/boost/commit/47ef674f752ad2cfeb581665cbea18ee6faecd2e#diff-09cc8a80ee9a61cf35f9c4498eefd67a   
  
https://github.com/boostorg/headers/commit/8fb634067e4cddbf932299f8cfdc9c8049a44d3f appears to be setting that elsewhere now, but it doesn't seem to work.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 17:22:16 UTC  
> Url: https://github.com/boostorg/build/issues/579#issuecomment-850868309  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
