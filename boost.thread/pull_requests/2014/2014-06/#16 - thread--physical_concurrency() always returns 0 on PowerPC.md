# #16 thread::physical_concurrency() always returns 0 on PowerPC [Closed]

> Username: harris-m  
> Created at: 2014-06-26 19:31:25 UTC  
> Updated at: 2014-07-01 17:27:44 UTC  
> Closed at: 2014-07-01 17:25:05 UTC  
> Url: https://github.com/boostorg/thread/pull/16  

The thread::physical_concurrency() function, located in src/pthread/thread.cpp, always returns 0 on the PowerPC platform. I believe this is because /proc/cpuinfo, which the function uses (on linux platforms) to determine the number of processors/cores on the machine, is formatted differently on PowerPC than it is on x86 and other platforms. Here is an example from the machine I am testing on (Ubuntu 14.04 LTS ppc64le):  
  
```  
$ cat /proc/cpuinfo  
processor   : 0  
cpu     : POWER7 (raw), altivec supported  
clock       : 3220.000000MHz  
revision    : 2.3 (pvr 003f 0203)  
  
processor   : 1  
cpu     : POWER7 (raw), altivec supported  
clock       : 3220.000000MHz  
revision    : 2.3 (pvr 003f 0203)  
  
processor   : 2  
cpu     : POWER7 (raw), altivec supported  
clock       : 3220.000000MHz  
revision    : 2.3 (pvr 003f 0203)  
  
processor   : 3  
cpu     : POWER7 (raw), altivec supported  
clock       : 3220.000000MHz  
revision    : 2.3 (pvr 003f 0203)  
  
processor   : 4  
cpu     : POWER7 (raw), altivec supported  
clock       : 3220.000000MHz  
revision    : 2.3 (pvr 003f 0203)  
  
processor   : 5  
cpu     : POWER7 (raw), altivec supported  
clock       : 3220.000000MHz  
revision    : 2.3 (pvr 003f 0203)  
  
processor   : 6  
cpu     : POWER7 (raw), altivec supported  
clock       : 3220.000000MHz  
revision    : 2.3 (pvr 003f 0203)  
  
processor   : 7  
cpu     : POWER7 (raw), altivec supported  
clock       : 3220.000000MHz  
revision    : 2.3 (pvr 003f 0203)  
  
timebase    : 512000000  
platform    : pSeries  
model       : IBM pSeries (emulated by qemu)  
machine     : CHRP IBM pSeries (emulated by qemu)  
```  
  
Note that the "physical id" and "core id" lines that the function expects are not present. The function does not account for this, and returns 0 as the number of processors/cores on the system. This causes test_physical_concurrency_lib.test and test_physical_concurrency.test to fail.  
  
I have created a pull request with a tentative fix, which uses the hardware_concurrency() function in the same file to determine the number of processors/cores on Power systems. It seems to resolve the two failing test cases without any regressions.

---

## Comment 1

> Username: harris-m  
> Created_at: 2014-07-01 17:27:44 UTC  
> Url: https://github.com/boostorg/thread/pull/16#issuecomment-47685450  

Closed in favor of #17

---
