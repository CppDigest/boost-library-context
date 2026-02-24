# #279 - Build (1.66.0) fails on Travis (OSX) [Closed]

> Username: mdyring  
> Created at: 2018-01-12 07:25:16 UTC  
> Updated at: 2018-01-12 16:26:35 UTC  
> Closed at: 2018-01-12 16:26:28 UTC  
> Url: https://github.com/boostorg/build/issues/279  

After upgrading from Boost 1.64.0 to 1.66.0, builds on Travis (OSX) fails with:   
  
ld: unknown option: --strip-all  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
  
According to build log, clang --version returns in Apple LLVM version 8.1.0 (clang-802.0.42).  
  
Is this a well known issue or something I should try to diagnose further?  
  
Thanks,  
Martin

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-01-12 16:00:00 UTC  
> Url: https://github.com/boostorg/build/issues/279#issuecomment-357277465  

AMDG  
  
On 01/12/2018 12:25 AM, Martin Dyring-Andersen wrote:  
> ld: unknown option: --strip-all  
> <snip>  
> Is this a well known issue or something I should try to diagnose further?  
>   
  
I think this is fixed in the current develop and master branches.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: mdyring  
> Created at: 2018-01-12 16:26:28 UTC  
> Updated at: 2018-01-12 16:26:35 UTC  
> Url: https://github.com/boostorg/build/issues/279#issuecomment-357285346  

Steven, first of all thank you for all your hard work on Boost Build.  
  
Regarding the issue, that sounds like great news. I'll wait until the 1.67 release to upgrade then.
