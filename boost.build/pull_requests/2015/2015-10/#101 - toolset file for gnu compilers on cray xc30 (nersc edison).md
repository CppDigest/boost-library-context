# #101 toolset file for gnu compilers on cray xc30 (nersc edison) [Closed]

> Username: cwsmith  
> Created at: 2015-10-29 01:24:40 UTC  
> Updated at: 2021-10-02 20:59:31 UTC  
> Closed at: 2021-06-11 01:54:29 UTC  
> Url: https://github.com/boostorg/build/pull/101  

I am very very new to boost-build, so take this PR with a massive crystal of salt.    
On a Cray XC30 (NERSC Edison) the gcc toolset appears to cause dynamic linking of the (somewhat complex) application we are building.  Using the cray toolset passes flags to the GNU compilers that it does not understand ('-h', in particular), sooo this is a seemingly successful attempt to use the cray compiler wrappers for static linking with the GNU programming environment module that Cray/NERSC provides.  
  
I can provide more details on the Cray-GNU environment as needed.  
  
Thank-you,  
Cameron

---

## Comment 1

> Username: stale[bot]  
> Created_at: 2021-05-29 16:21:56 UTC  
> Url: https://github.com/boostorg/build/pull/101#issuecomment-850859516  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.

---

## Comment 2

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:30 UTC  
> Url: https://github.com/boostorg/build/pull/101#issuecomment-932819658  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
