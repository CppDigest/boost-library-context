# #175 Fix errors when using platforms with no devices [Merged]

> Username: kylelutz  
> Created at: 2014-07-08 01:32:56 UTC  
> Updated at: 2014-07-08 04:30:50 UTC  
> Merged at: 2014-07-08 01:57:16 UTC  
> Closed at: 2014-07-08 01:57:16 UTC  
> Url: https://github.com/boostorg/compute/pull/175  

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
> Created_at: 2014-07-08 02:03:02 UTC  
> Url: https://github.com/boostorg/compute/pull/175#issuecomment-48264011  

[![Coverage Status](https://coveralls.io/builds/942614/badge)](https://coveralls.io/builds/942614)  
  
Coverage decreased (-0.03%) when pulling **73443e01112c7c9098487732a1ab38f526186574 on fix-platform-empty-devices** into **5d6921f16213eeee042816f64bdacb4bfc378693 on develop**.

---
