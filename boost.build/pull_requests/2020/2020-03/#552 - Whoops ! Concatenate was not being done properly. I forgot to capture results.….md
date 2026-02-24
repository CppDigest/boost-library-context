# #552 Whoops ! Concatenate was not being done properly. I forgot to capture results.… [Merged]

> Username: eldiener  
> Created at: 2020-03-21 07:03:03 UTC  
> Updated at: 2021-10-02 21:13:25 UTC  
> Merged at: 2020-03-21 12:46:47 UTC  
> Closed at: 2020-03-21 12:46:47 UTC  
> Url: https://github.com/boostorg/build/pull/552  

… This led to // in a file spec, which did not seem to harm anything but still needs to be fixed. Everything was still working but an executable path of "c:/somepath//someprogram.exe" etc. is not healthy in Windows even if the command processor allowed it.

---
