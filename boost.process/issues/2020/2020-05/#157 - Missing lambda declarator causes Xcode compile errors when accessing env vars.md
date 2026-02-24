# #157 - Missing lambda declarator causes Xcode compile errors when accessing env vars [Closed]

> Username: acronce  
> Created at: 2020-05-13 14:44:41 UTC  
> Updated at: 2020-05-20 14:05:43 UTC  
> Closed at: 2020-05-20 14:05:43 UTC  
> Url: https://github.com/boostorg/process/issues/157  

I'm seeing compile errors under Xcode 11.4.1 when attempting to access environment variables . For example:  
  
bp::environment environment(boost::this_process::environment());  
environment["foo"] = "bar";  
  
The second line (setting the "foo" env var) produces this compile error:  
  
Return type 'bool' must match previous return type 'int' when lambda expression has unspecified explicit return type  
  
I've fixed this by setting an explicit lambda declarator in commit 3aba1f6eb16d3b4780eb212e2bd6265e4e5aa36c of my fork of boost::process:  
  
https://github.com/acronce/process  
  
I'm not sure what the process is for a pull request, but it would be great if the maintainer pulled in this fix.

---

## Comment 1

> Username: acronce  
> Created at: 2020-05-14 15:29:42 UTC  
> Url: https://github.com/boostorg/process/issues/157#issuecomment-628711357  

Note that I went ahead and made a pull request for this.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2020-05-20 14:05:43 UTC  
> Url: https://github.com/boostorg/process/issues/157#issuecomment-631495154  

Closing in favor of the PR.
