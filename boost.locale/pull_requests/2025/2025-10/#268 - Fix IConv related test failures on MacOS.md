# #268 Fix IConv related test failures on MacOS [Merged]

> Username: Flamefire  
> Created at: 2025-10-23 09:15:54 UTC  
> Updated at: 2025-10-25 13:40:27 UTC  
> Merged at: 2025-10-25 13:40:25 UTC  
> Closed at: 2025-10-25 13:40:25 UTC  
> Url: https://github.com/boostorg/locale/pull/268  

The system IConv on macOS is not GNU conformant and uses e.g. WTF-8 instead of UTF-8 and generates overlong-sequences.  
  
Also the encoding support might be limitted leading to silently wrong results, like replacing characters by question marks.  
  
This causes failures in the test.  
  
Try hard to detect that and handle the then expected results.

---
