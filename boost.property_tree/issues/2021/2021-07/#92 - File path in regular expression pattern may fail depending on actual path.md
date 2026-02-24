# #92 - File path in regular expression pattern may fail depending on actual path [Closed]

> Username: s13n  
> Created at: 2021-07-23 21:02:17 UTC  
> Updated at: 2023-11-15 12:34:08 UTC  
> Closed at: 2023-11-15 12:34:07 UTC  
> Url: https://github.com/boostorg/property_tree/issues/92  

In test/CMakeLists.txt line 12, a Regex pattern is being constructed from a variable that holds a system-dependent file path. If that path contains characters that are special in a Regex pattern, a failure results.  
  
Example: A path containing plus signs '+'  
  
Bottom line: Don't construct a Regex pattern from unknown text.

---

## Comment 1

> Username: ashtum  
> Created at: 2023-11-15 12:34:07 UTC  
> Url: https://github.com/boostorg/property_tree/issues/92#issuecomment-1812459276  

Fixed in https://github.com/boostorg/property_tree/commit/3209276068bec51410bb2c439df090bbbe3320f0.
