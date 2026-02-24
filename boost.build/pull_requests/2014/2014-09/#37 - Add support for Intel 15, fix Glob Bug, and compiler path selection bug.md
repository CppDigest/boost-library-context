# #37 Add support for Intel 15, fix Glob Bug, and compiler path selection bug. [Closed]

> Username: jzmaddock  
> Created at: 2014-09-21 16:15:43 UTC  
> Updated at: 2021-10-02 22:35:45 UTC  
> Closed at: 2014-09-22 19:09:25 UTC  
> Url: https://github.com/boostorg/build/pull/37  

This patch fixes 3 issues:  
- Adds support for Intel-15 so MSVC version can be auto-selected.  
- Changes GLOB to path.glob as for some reason the GLOB rule always fails when used in this context (discovered when testing this patch).  
- Changes selection logic for target-types variable - previous code used the value of PROCESSOR_ARCHITECTURE to detect this, but that appears to only tell you which sub-system bjam is running in.  For example on my system bjam is a 32-bit app (default for an msvc build), but I only have the Intel 64-bit compiler installed (under /intel64/).  New logic checks whether the intel64 compiler exists and uses that if it is - Intel's installer won't install this on 32 bit platforms in which case the glob fails and things fall back to the ia32-intel64 cross compiler when address-model=64 is specified.

---

## Comment 1

> Username: vprus  
> Created_at: 2014-09-22 19:09:25 UTC  
> Url: https://github.com/boostorg/build/pull/37#issuecomment-56424212  

Thanks John! Merged.

---
