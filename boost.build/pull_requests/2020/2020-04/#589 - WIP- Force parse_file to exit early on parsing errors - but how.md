# #589 WIP: Force parse_file to exit early on parsing errors - but how? [Closed]

> Username: mloskot  
> Created at: 2020-04-30 23:56:52 UTC  
> Updated at: 2021-10-02 20:59:23 UTC  
> Closed at: 2021-06-11 01:54:38 UTC  
> Url: https://github.com/boostorg/build/pull/589  

This is a quick incomplete evaluation of how to report `status` code from calls rooted at `parse_file` in order to let quit b2 as early as possible.  
  
PROBLEM:  
How to early abort `function_run` on error?  
The status returned from `parse_file` there is ignored.  
  
Currently Jamfile parsing errors do not stop b2 which continues and reports non-zero exit code at the end of the whole build.  
This is not user-friendly behaviour especially if user ignores exit code and ignores parsing errors reported at the top of the b2 output (e.g. issue #585).  
  
By the way, `-q` option for quick quit does not stop b2 on parsing errors, it is not related to this phase at all.  
If early exit on parsing errors is feasible, then `-q` semantics could be extended to those errors too.

---

## Comment 1

> Username: stale[bot]  
> Created_at: 2021-05-29 16:21:46 UTC  
> Url: https://github.com/boostorg/build/pull/589#issuecomment-850859485  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.

---

## Comment 2

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:22 UTC  
> Url: https://github.com/boostorg/build/pull/589#issuecomment-932819634  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
