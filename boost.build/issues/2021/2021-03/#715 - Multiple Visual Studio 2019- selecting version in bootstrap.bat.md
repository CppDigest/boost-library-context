# #715 - Multiple Visual Studio 2019: selecting version in bootstrap.bat [Open]

> Username: tobias-loew  
> Created at: 2021-03-12 10:44:33 UTC  
> Updated at: 2021-05-29 16:22:54 UTC  
> Url: https://github.com/boostorg/build/issues/715  

Hi,  
  
I've got multiple versions of Visual Studio 2019 on my build machine.  
Currently bootstrap.bat always selects the "latest" one (which is usually the current Preview version).  
It would be cool having a command line option to specify the version-range in the same way vswhere expects it.  
Additionally, disabling of preview versions would also be a useful option  
  
bootstrap.bat --version [16.8,16.9) --no-preview  
  
Tobias

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:23 UTC  
> Url: https://github.com/boostorg/build/issues/715#issuecomment-850859591  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
