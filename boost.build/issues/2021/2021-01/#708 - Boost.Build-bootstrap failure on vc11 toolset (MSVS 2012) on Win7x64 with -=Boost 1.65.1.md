# #708 - Boost.Build/bootstrap failure on vc11 toolset (MSVS 2012) on Win7x64 with >=Boost 1.65.1 [Closed]

> Username: VolkerBartheld  
> Created at: 2021-01-30 14:31:10 UTC  
> Updated at: 2021-02-25 07:58:47 UTC  
> Closed at: 2021-01-30 19:39:29 UTC  
> Url: https://github.com/boostorg/build/issues/708  

Hi!  
  
I'm getting issues trying to run bootstrap.bat on my Win7x64 MSVS 2012 system. Yeah, it's quite old, but even the [Boost 1.75 release notes](https://www.boost.org/users/history/version_1_75_0.html) tell me that it was tested on Visual C++: 7.1, 8.0, 9.0, 10.0, 11.0, 12.0, 14.0, 14.1. My last successful build happened with version 1.64.0 (also succeeds building the libraries, I tried .\b2 --build-type=complete stage), every subsequent release fails the Boost.Build step. I have attached the relevant log files below.  
  
Thanks for any help!  
  
Greets,  
Volker  
  
P.S.: Bootstrap 1.75.0 seems to print directly to the console and not pipe output to bootstrap.log anymore. Don't know if this is expected behaviour.  
  
[bootstrap.1.62.0.log](https://github.com/boostorg/build/files/5898068/bootstrap.1.62.0.log)  
[bootstrap.1.64.0.log](https://github.com/boostorg/build/files/5898069/bootstrap.1.64.0.log)  
[bootstrap.1.65.1.log](https://github.com/boostorg/build/files/5898070/bootstrap.1.65.1.log)  
[bootstrap.1.75.0.log](https://github.com/boostorg/build/files/5898085/bootstrap.1.75.0.log)

---

## Comment 1

> Username: grafikrobot  
> Created at: 2021-01-30 19:39:29 UTC  
> Url: https://github.com/boostorg/build/issues/708#issuecomment-770269516  

Release 4.0 onward of B2 requires a C++11 capable compiler to build the engine. And hence to bootstrap Boost: https://github.com/boostorg/build/releases/tag/4.0.0 .. Which means Boost releases from 1.72 onward requires C++11 to bootstrap. But can use any tested C++ to build after the bootstrap.

---

## Comment 2

> Username: VolkerBartheld  
> Created at: 2021-02-25 07:58:47 UTC  
> Url: https://github.com/boostorg/build/issues/708#issuecomment-785699324  

Thanks for explaining. You should perhaps mention this in the Boost release notes/install howto. "tested on Visual C++: 7.1, 8.0, 9.0, 10.0, 11.0, 12.0, 14.0, 14.1." does not really indicate you would need to have C++11 available für building the libraries. At least it was not clear to me.
