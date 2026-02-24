# #328 - Problems with project.extension [Open]

> Username: swatanabe  
> Created at: 2018-08-08 19:51:57 UTC  
> Updated at: 2021-06-26 03:09:59 UTC  
> Url: https://github.com/boostorg/build/issues/328  

This rule currently has many issues:  
  
- The extension project inherits some things from Jamroot.  I don't understand why.  
- Extension projects are not usable from config files (even though the documentation says that extension projects can be initialized with using).  This is a result of inheriting things from Jamroot, which doesn't exist yet when the config files are loaded.  
- project.extension searches for the project root incorrectly (it skips project-config.jam).  This breaks down if there is a project-config.jam above Jamroot, instead of alongside Jamroot.  
- Is it even needed in the first place?  standalone projects (with no location) work just fine.  
- The "location" that it uses is very strange and often doesn't actually exist.  If it does exist, then the behavior is likely to be surprising.  
- It does most of its work in the caller's module.  Only the `project` rule actually needs to be run in the caller's module.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:23 UTC  
> Url: https://github.com/boostorg/build/issues/328#issuecomment-868936424  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
