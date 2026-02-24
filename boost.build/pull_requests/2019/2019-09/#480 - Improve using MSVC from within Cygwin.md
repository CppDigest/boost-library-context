# #480 Improve using MSVC from within Cygwin [Merged]

> Username: haubi  
> Created at: 2019-09-16 16:27:16 UTC  
> Updated at: 2021-10-02 21:13:48 UTC  
> Merged at: 2019-09-29 14:09:02 UTC  
> Closed at: 2019-09-29 14:09:02 UTC  
> Url: https://github.com/boostorg/build/pull/480  

Recent MSVC versions want to be located using `vswhere.exe` rather than some `VSxxCOMNTOOLS` environment variable. With the Cygwin `b2.exe`, this breaks along issue #459 because Cygwin `/bin/sh` does perform a `PATH` search when there is no _forward_ slash in the filename to be executed.  
Beyond that, locating `vswhere.exe` itself does require an environment variable whose name is invalid to Cygwin, rendering users that want to have full control over the environment variables unable to restore `ProgramFiles(x86)` for `b2.exe`.  
For more details please refer to the inline comments.  
Thanks!

---
