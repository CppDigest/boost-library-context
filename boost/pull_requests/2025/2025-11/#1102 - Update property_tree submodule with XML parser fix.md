# #1102 Update property_tree submodule with XML parser fix [Closed]

> Username: memurro000  
> Created at: 2025-11-20 14:24:40 UTC  
> Updated at: 2025-11-20 22:44:55 UTC  
> Closed at: 2025-11-20 22:39:09 UTC  
> Url: https://github.com/boostorg/boost/pull/1102  

## Fix use-of-uninitialized-value in XML parser  
  
**Problem:** MemorySanitizer detects use-of-uninitialized-value at line 96 in `xml_parser_read_rapidxml.hpp` when parsing malformed XML input through GraphML parser (issue boostorg/property_tree#131).  
  
**Root cause:** The XML parser attempts to read from streams that are in bad state or contain uninitialized data, which leads to undefined behavior in RapidXML.  
  
**Solution:** Add early stream validation in `read_xml_internal()`:  
- Check stream state with `stream.good()` before any operations  
- Verify non-empty input with `stream.peek() != eof()`  
- Throw appropriate `xml_parser_error` for invalid inputs  
  
**Testing:** The fix resolves the MemorySanitizer warning while maintaining backward compatibility. Empty streams and bad stream states now throw clear exceptions instead of causing undefined behavior.  
  
**Files changed:**  
- `libs/property_tree` submodule updated with the security fix  
  
Fixes: boostorg/property_tree#131

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-11-20 20:19:02 UTC  
> Url: https://github.com/boostorg/boost/pull/1102#issuecomment-3559895279  

This fix needs to be applied in boostorg/property_tree, not here. Submodules are updated automatically by a script.

---

## Comment 2

> Username: memurro000  
> Created_at: 2025-11-20 22:44:55 UTC  
> Url: https://github.com/boostorg/boost/pull/1102#issuecomment-3560450951  

The fix as requested has been moved to:  
https://github.com/boostorg/property_tree/pull/132

---
