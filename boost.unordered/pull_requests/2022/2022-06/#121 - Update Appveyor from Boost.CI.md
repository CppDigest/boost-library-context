# #121 Update Appveyor from Boost.CI  [Merged]

> Username: Flamefire  
> Created at: 2022-06-08 13:29:58 UTC  
> Updated at: 2022-06-22 15:36:37 UTC  
> Merged at: 2022-06-09 18:02:38 UTC  
> Closed at: 2022-06-09 18:02:38 UTC  
> Url: https://github.com/boostorg/unordered/pull/121  

Extracted from #114 to make the changes easier to handle

---

## Comment 1

> Username: Flamefire  
> Created_at: 2022-06-09 08:03:48 UTC  
> Url: https://github.com/boostorg/unordered/pull/121#issuecomment-1150803010  

@cmazakas This and #114 are ready now. I'd suggest to merge them both at the same time (at least on GHA the 2nd commit/merge will cancel the CI run started by the first) and then merge develop to master, potentially after adjusting the `.codecov.yml` to your liking, see #114 for details. Only then the codecov settings will be in effect.

---

## Comment 2

> Username: pdimov  
> Created_at: 2022-06-21 17:11:37 UTC  
> Url: https://github.com/boostorg/unordered/pull/121#issuecomment-1162029899  

@Flamefire In order to fix the Cygwin failure (marked as allowed now), we need to pass `include=` to `b2`, but at the moment boost-ci doesn't seem to have the `B2_INCLUDE` option: https://github.com/boostorg/boost-ci/blob/a5712f1268770e1a32e352d485247d0460145a3f/ci/build.bat#L13-L18

---

## Comment 3

> Username: Flamefire  
> Created_at: 2022-06-21 22:03:01 UTC  
> Url: https://github.com/boostorg/unordered/pull/121#issuecomment-1162409971  

IMO this needs to be set in the Jamfiles. I wouldn't expect users to need to know which paths to pass to `include=`.

---

## Comment 4

> Username: pdimov  
> Created_at: 2022-06-21 23:58:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/121#issuecomment-1162473774  

I am the user in this case and I know which paths I need to pass to get Cygwin to work.

---

## Comment 5

> Username: Flamefire  
> Created_at: 2022-06-22 15:36:36 UTC  
> Url: https://github.com/boostorg/unordered/pull/121#issuecomment-1163272772  

Yes, so this are 2 separate issues:  
  
- The build.bat needs to handle B2_INCLUDE --> I opened https://github.com/boostorg/boost-ci/pull/173  
- Either the Jamfiles or the CI script here needs to be updated for Cygwin  
  
IMO the Jamfiles need to be updated so a user wanting to test this library doesn't need to fiddle with B2 options just to get this library to find its own headers. The new Cygwin is just 1 instance of a platform where `#include "foo.h"` doesn't search the folder containing that compilation unit for `foo.h`, there might be others. I would leave users need to set `include=bar` only for stuff like system-OpenSSL or so when that is required, and not for "our" headers

---
