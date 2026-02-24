# #1120 - boost does not cross-compile for qnx (at least x86_64) since 1.86.0 [Open]

> Username: simonCor  
> Created at: 2026-01-19 07:02:10 UTC  
> Updated at: 2026-01-19 07:02:10 UTC  
> Url: https://github.com/boostorg/boost/issues/1120  

Cross compiling boost release 1.86.0 on QNX SDP 7.1 (qcc 8.3.0 (with gcc backend)) results in Segmentation Fault.  
  
I tried the following versions:  
1.81.0 works ✅  
1.85.0 works ✅  
1.86.0 seg fault ❌  
1.87.0 seg fault ❌  
1.90.0 seg fault ❌  
  
I used the following comands on a freshly download version:  
`./bootstrap`  
`./b2 -j$(nproc)   toolset=qcc   target-os=qnx   architecture=x86 address-model=64   variant=release   threading=multi threadapi=pthread   link=static runtime-link=static   --with-system --with-filesystem --with-thread --with-chrono --with-date_time --with-regex --with-program_options`  
  
The only output I see on the terminal is:  
`Segmentation fault (core dumped)`  
  
  
I cross compile on the following host (lsb_release -a):  
```  
No LSB modules are available.  
Distributor ID: Ubuntu  
Description:    Ubuntu 22.04.3 LTS  
Release:        22.04  
Codename:       jammy  
```
