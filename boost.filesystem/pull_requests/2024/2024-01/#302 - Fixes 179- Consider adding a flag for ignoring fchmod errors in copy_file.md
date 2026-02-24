# #302 Fixes 179: Consider adding a flag for ignoring fchmod errors in copy_file [Closed]

> Username: dsingh-mw  
> Created at: 2024-01-11 05:49:32 UTC  
> Updated at: 2024-01-12 06:16:12 UTC  
> Closed at: 2024-01-12 06:16:12 UTC  
> Url: https://github.com/boostorg/filesystem/pull/302  

Added a new flag 'skip_fchmod_failure ' in copy_options to ignore errors from fchmod. This flag when used will suppress the errors arising from 'fchmod'   
A test is also added, which mocks a fchmod failure to test whether the fchmod failure is suppressed when the 'skip_fchmod_failure' flag is used .

---
