# #1 Fix compilation errors seen with MSVC [Closed]

> Username: DanielaE  
> Created at: 2014-01-08 16:09:47 UTC  
> Updated at: 2015-01-10 15:54:21 UTC  
> Closed at: 2014-01-08 21:21:24 UTC  
> Url: https://github.com/boostorg/program_options/pull/1  

Previous fix 4ae33ce for ticket #6797 introduced a new problem with Visual Studio compilers about two missing methods in 'common_config_file_iterator'. Adding those seemingly not required methods as empty methods fixes the issue. As an added bonus, the bogus warnings about DLL-interfaces get a silencing treatment, too.  
  
Tests were run with vc10, vc11, and vc12. Without this fix, the affected test cases fail on all 3 compilers. With the fix in place, all tests pass.  
  
Signed-off-by: Daniela Engert dani@ngrt.de

---

## Comment 1

> Username: vprus  
> Created_at: 2014-01-08 21:21:24 UTC  
> Url: https://github.com/boostorg/program_options/pull/1#issuecomment-31879177  

Thanks for the patch; I've checked it in to develop branch.

---
