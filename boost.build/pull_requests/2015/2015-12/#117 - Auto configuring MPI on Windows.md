# #117 Auto configuring MPI on Windows [Merged]

> Username: praetorian20  
> Created at: 2015-12-23 07:52:00 UTC  
> Updated at: 2021-10-02 22:19:09 UTC  
> Merged at: 2016-07-15 15:41:47 UTC  
> Closed at: 2016-07-15 15:41:47 UTC  
> Url: https://github.com/boostorg/build/pull/117  

Microsoft has a new MPI implementation available - [Microsoft MPI](https://msdn.microsoft.com/en-us/library/windows/desktop/bb524831%28v=vs.85%29.aspx). There are now two installers, one for the MPI executor and another for the MPI SDK (which contains the headers and static libraries), and these install to different locations by default.  
  
The jam file has been changed so that MS-MPI is first checked, if found use that as the MPI executor/SDK. If not found, the MS Compute Cluster Pack is checked next.  
  
The changes were tested on Windows 10 x64 with VS2015.  
  
Also stripped trailing whitespace from the file.

---

## Comment 1

> Username: jhunold  
> Created_at: 2016-07-14 15:11:37 UTC  
> Url: https://github.com/boostorg/build/pull/117#issuecomment-232694638  

Tested the patch with the latest Microsoft MPI and Visual Studio 2015. Works.   
I'd like to merge this, but "Also stripped trailing whitespace from the file." is obfuscating the changes. Can you please split this into two commits? One removing the whitespace first and then making the real changes. Otherwise it is really hard to figure out what has changed.

---

## Comment 2

> Username: praetorian20  
> Created_at: 2016-07-15 06:09:56 UTC  
> Url: https://github.com/boostorg/build/pull/117#issuecomment-232867767  

Done. Also made the MPI changes commit message a lot more descriptive.

---

## Comment 3

> Username: jhunold  
> Created_at: 2016-07-15 15:42:07 UTC  
> Url: https://github.com/boostorg/build/pull/117#issuecomment-232988269  

Thanks for the cleanup.

---
