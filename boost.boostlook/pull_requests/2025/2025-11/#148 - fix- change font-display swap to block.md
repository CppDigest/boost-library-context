# #148 fix: change font-display swap to block [Merged]

> Username: julioest  
> Created at: 2025-11-20 19:31:28 UTC  
> Updated at: 2025-11-20 19:31:37 UTC  
> Merged at: 2025-11-20 19:31:37 UTC  
> Closed at: 2025-11-20 19:31:37 UTC  
> Url: https://github.com/boostorg/boostlook/pull/148  

## Problem  
Headers flash system font then swap to Noto Sans  
  
## Solution  
Change `font-display: swap` to `font-display: block` for all Noto Sans fonts to prevent text rendering until fonts are loaded.

---
