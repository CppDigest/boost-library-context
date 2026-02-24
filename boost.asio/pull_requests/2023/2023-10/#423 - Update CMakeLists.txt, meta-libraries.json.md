# #423 Update CMakeLists.txt, meta/libraries.json [Open]

> Username: pdimov  
> Created at: 2023-10-26 12:07:17 UTC  
> Updated at: 2023-10-27 12:54:41 UTC  
> Url: https://github.com/boostorg/asio/pull/423  

Updates `meta/libraries.json` with the new `cxxstd` level of 11 and regenerates CMakeLists.txt (using `boostdep --cmake asio`) to pick up the new requirement and the new dependency information.  
  
This is necessary to resolve CI failures in dependent libraries because e.g. Boost.Chrono is no longer used, but the old CMakeLists.txt links to it.

---

## Comment 1

> Username: anarthal  
> Created_at: 2023-10-27 12:54:41 UTC  
> Url: https://github.com/boostorg/asio/pull/423#issuecomment-1782869358  

Much needed here.

---
