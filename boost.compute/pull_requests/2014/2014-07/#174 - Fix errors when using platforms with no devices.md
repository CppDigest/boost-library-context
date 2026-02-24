# #174 Fix errors when using platforms with no devices [Closed]

> Username: kylelutz  
> Created at: 2014-07-07 14:59:35 UTC  
> Updated at: 2014-07-08 04:30:50 UTC  
> Closed at: 2014-07-08 01:33:12 UTC  
> Url: https://github.com/boostorg/compute/pull/174  

This fixes errors caused when handling OpenCL platforms with  
no devices. Now the platform::devices() method will properly  
return an empty vector without throwing an exception when such  
a platform is encountered.  
  
Previously, default_device() would throw an exception when  
confronted with a platform with no devices even if devices  
from other platforms on the system were available.  
  
Thanks to Godeffroy Valet for reporting this issue.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-07-07 15:33:34 UTC  
> Url: https://github.com/boostorg/compute/pull/174#issuecomment-48194641  

[![Coverage Status](https://coveralls.io/builds/940593/badge)](https://coveralls.io/builds/940593)  
  
Coverage decreased (-0.03%) when pulling **73443e01112c7c9098487732a1ab38f526186574 on fix-platform-empty-devices** into **bfe2bdd7f77644350d76f39a0be49feff79653a9 on master**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-07-08 01:33:12 UTC  
> Url: https://github.com/boostorg/compute/pull/174#issuecomment-48262409  

See PR #175

---
