# #522 Fixes CI errors in GHA, Appveyor and Drone [Merged]

> Username: anarthal  
> Created at: 2025-09-19 12:53:11 UTC  
> Updated at: 2025-09-20 11:43:54 UTC  
> Merged at: 2025-09-20 11:43:54 UTC  
> Closed at: 2025-09-20 11:43:54 UTC  
> Url: https://github.com/boostorg/config/pull/522  

* Updates actions/checkout from v3 to v5  
* Removes the ci-skip action, as it's now built into GHA  
* Removes the Windows 2019 builds from GHA, since these runners are no longer available  
* Adds a MSVC 14.2 build to Appveyor, replacing those removed from GHA  
* Adds missing dependencies to the Appveyor build  
* Removes the --experimental-wasm-threads flag from the wasm builds, which was removed in Node 20.0  
* Updates the Drone gcc-13 build to use Ubuntu 24.04, which is LTS

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2025-09-20 08:24:06 UTC  
> Url: https://github.com/boostorg/config/pull/522#issuecomment-3314775302  

I don't have strong views either way, but I would be inclined to remove appveyor altogether and move it all to drone?

---

## Comment 2

> Username: anarthal  
> Created_at: 2025-09-20 10:22:09 UTC  
> Url: https://github.com/boostorg/config/pull/522#issuecomment-3314881186  

I do agree, since Appveyor is painfully slow. But the migration is not trivial for me, as I've never run MSVC builds before 14.0 in Drone before.  
  
The only build which was failing now was a MSVC 14.1 that I added to Appveyor for completeness, which we didn't use to have. I've removed it, returning to the status quo, and this should (hopefully) work.  
  
My proposal is merging this once everything is green, so new changes like the updated Boost version header can be merged, and I'll submit another PR to get rid of Appveyor once I discover the best way to do it.

---
