# #23 Add `--help` and refactoring for better error handling [Open]

> Username: Flamefire  
> Created at: 2024-10-06 12:28:16 UTC  
> Updated at: 2024-10-06 16:36:32 UTC  
> Url: https://github.com/boostorg/boostdep/pull/23  

Add `--help` option and factor out a `boost_module` class  
  
Introduces a class for handling e.g. the sub-paths for Boost modules.  
This allows to keep the related logic more focused and consistent.  
  
Also use that for validating the command line arguments expecting modules.  
  
  
Motivation was that after passing e.g. `libs/locale` as an argument to `--cmake` which yielded a CML that was missing all files, dependencies and C++ standard setting instead of showing an error.

---
