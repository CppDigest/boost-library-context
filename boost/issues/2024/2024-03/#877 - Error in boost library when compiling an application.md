# #877 - Error in boost library when compiling an application [Open]

> Username: SpirosZafeiris  
> Created at: 2024-03-31 15:33:33 UTC  
> Updated at: 2025-09-04 05:58:15 UTC  
> Url: https://github.com/boostorg/boost/issues/877  

Hello everyone.  
  
I am trying to compile an external cpp project that uses boost (linked and built with CMake).   
  
However the following error is thrown while compiling the project.  
  
![image](https://github.com/boostorg/boost/assets/114661411/c8c02f1d-7e18-4a7d-80f4-85d5b2a3f0c6)  
  
I am on Debian testing (trixie) and the boost library is installed via `apt install libboost1.83-all-dev`.   
  
In my laptop I have dnf as a package manager, and I installed with `dnf install boost-devel` (it installs 1.81.0 version) and everything compiled (and ran) fine.   
  
Am I missing something while installing the library?  
  
Thanks in advance.

---

## Comment 1

> Username: mclow  
> Created at: 2024-03-31 17:45:01 UTC  
> Url: https://github.com/boostorg/boost/issues/877#issuecomment-2028841548  

`<algorithm>` is a standard library header file, not part of boost.  I suspect that something in your setup is incorrect.

---

## Comment 2

> Username: nigels-com  
> Created at: 2025-09-04 05:58:15 UTC  
> Url: https://github.com/boostorg/boost/issues/877#issuecomment-3252033342  

This issue ought to be closed.  
It's clear that boost isn't at issue here.
