# #343 - [msvc] Remove need for setup scripts on each action. [Open]

> Username: grafikrobot  
> Created at: 2018-09-26 07:37:09 UTC  
> Updated at: 2021-06-26 03:09:56 UTC  
> Url: https://github.com/boostorg/build/issues/343  

Talking to  Robert S. he mentioned that we could avoid running the vcvars script for each compiler invocation by instead: running the vcvars once on configure, extracting the compiler env vars, converting them to regular build options (i.e. include, libdirs, etc), clearing the env for each compile invocation. This depends on https://github.com/boostorg/build/issues/342

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:21 UTC  
> Url: https://github.com/boostorg/build/issues/343#issuecomment-868936410  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
