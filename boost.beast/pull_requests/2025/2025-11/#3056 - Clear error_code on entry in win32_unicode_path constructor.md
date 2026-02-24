# #3056 Clear error_code on entry in win32_unicode_path constructor [Open]

> Username: ssam18  
> Created at: 2025-11-12 20:56:16 UTC  
> Updated at: 2025-11-30 15:57:59 UTC  
> Url: https://github.com/boostorg/beast/pull/3056  

Fixes #3035  
  
### Issue  
  
The `win32_unicode_path` constructor accepts an `error_code&` parameter but wasn't clearing it on successful path conversion. This led to callers retaining stale error values even when operations succeeded.  
  
### Problem  
  
As reported in #3035, when using `file.open()` with a pre-set error code (e.g., initialized to `error::not_found` as a default), the error would persist even after successful file operations. This is inconsistent with std::filesystem conventions where error_code parameters are cleared on success.  
  
### Solution    
  
Added `ec = {};` at the beginning of the constructor to ensure the error_code is always in a known state - either cleared for success or properly set for failure.  
  
This aligns with:  
- The std::filesystem API convention (e.g., `std::filesystem::create_directory`)  
- The recommendation from @vinniefalco to clear `ec` immediately upon entry to avoid these kinds of issues  
- Beast's own `file_win32::open` which already clears ec on line 222  
  
### Testing  
  
Windows-specific change. The fix is minimal and follows established error handling patterns in the codebase.

---

## Comment 1

> Username: ssam18  
> Created_at: 2025-11-30 14:49:09 UTC  
> Url: https://github.com/boostorg/beast/pull/3056#issuecomment-3592624132  

@vinniefalco  - Can you please get a chance to review this PR?

---

## Comment 2

> Username: ashtum  
> Created_at: 2025-11-30 15:57:59 UTC  
> Url: https://github.com/boostorg/beast/pull/3056#issuecomment-3592738393  

Please review all file-related operations to ensure they clear `ec` on success, and also determine how we can add appropriate tests to confirm this.

---
