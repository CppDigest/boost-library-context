# #809 Simplified travis and added some new options [Open]

> Username: Zialus  
> Created at: 2019-01-02 02:38:36 UTC  
> Updated at: 2019-09-15 22:31:35 UTC  
> Url: https://github.com/boostorg/compute/pull/809  

Things left to do:  
  
- [ ] figure out why windows doesn't see any OpenCL platform/device  
- [ ] cache boost installation on windows  
- [X] cache boost installation on osx/gcc combo

---

## Comment 1

> Username: coveralls  
> Created_at: 2019-01-02 05:19:24 UTC  
> Url: https://github.com/boostorg/compute/pull/809#issuecomment-450788700  

[![Coverage Status](https://coveralls.io/builds/20862868/badge)](https://coveralls.io/builds/20862868)  
  
Coverage remained the same at 84.02% when pulling **eecaba169acbfc3cd33ae54e5f0ca53fcabc0ad0 on Zialus:travis-ci** into **2135633bc7f6bddaf09ae409a63a83174e89c881 on boostorg:develop**.

---

## Comment 2

> Username: Zialus  
> Created_at: 2019-01-02 15:24:40 UTC  
> Updated_at: 2019-01-02 18:35:30 UTC  
> Url: https://github.com/boostorg/compute/pull/809#issuecomment-450891952  

@jszuppe could you help out with the caching of boost on windows?  
  
I'm not sure what to do about the OpenCL part though. It's working fine on appveyor. https://ci.appveyor.com/project/Zialus/compute/builds/21322560

---

## Comment 3

> Username: jszuppe  
> Created_at: 2019-01-02 18:15:14 UTC  
> Url: https://github.com/boostorg/compute/pull/809#issuecomment-450939524  

> @jszuppe could you help out with the caching boost on windows?  
  
Sure, but first we need to make it detect an OpenCL device. For that you can use clinfo, no need to build Boost.Compute.  
  
> I'm not sure what to do about the OpenCL part though. It's working fine on appveyor.   
  
Potential problems: MinGW-W64, intel 16.1.1 drivers (lack of support for used CPU). CMake can locate `OpenCL.lib` file installed using NuGet, I guess that's only ICD. The problem may be in locating `.dll` file provided by Intel with OpenCL implementation.

---

## Comment 4

> Username: Zialus  
> Created_at: 2019-01-03 04:04:07 UTC  
> Url: https://github.com/boostorg/compute/pull/809#issuecomment-451051568  

Result of running `wmic cpu list full`  
```  
AddressWidth=64  
Architecture=9  
Availability=3  
Caption=Intel64 Family 6 Model 63 Stepping 0  
ConfigManagerErrorCode=  
ConfigManagerUserConfig=  
CpuStatus=1  
CreationClassName=Win32_Processor  
CurrentClockSpeed=2300  
CurrentVoltage=  
DataWidth=64  
Description=Intel64 Family 6 Model 63 Stepping 0  
DeviceID=CPU0  
ErrorCleared=  
ErrorDescription=  
ExtClock=  
Family=1  
InstallDate=  
L2CacheSize=  
L2CacheSpeed=  
LastErrorCode=  
Level=6  
LoadPercentage=9  
Manufacturer=GenuineIntel  
MaxClockSpeed=2300  
Name=Intel(R) Xeon(R) CPU @ 2.30GHz  
OtherFamilyDescription=  
PNPDeviceID=  
PowerManagementCapabilities=  
PowerManagementSupported=FALSE  
ProcessorId=1F8BFBFF000306F0  
ProcessorType=3  
Revision=16128  
Role=CPU  
SocketDesignation=CPU 1  
Status=OK  
StatusInfo=3  
Stepping=  
SystemCreationClassName=Win32_ComputerSystem  
SystemName=PACKER-5BEC3ACC  
UniqueId=  
UpgradeMethod=1  
Version=  
VoltageCaps=0  
```

---

## Comment 5

> Username: Zialus  
> Created_at: 2019-01-31 11:55:05 UTC  
> Url: https://github.com/boostorg/compute/pull/809#issuecomment-459317695  

hey @jszuppe did you end up figuring something out about this?  
  
I guess I could open a thread on travis [community forum](https://travis-ci.community/c/environments/windows) if you want me to.

---

## Comment 6

> Username: Zialus  
> Created_at: 2019-08-15 19:16:53 UTC  
> Url: https://github.com/boostorg/compute/pull/809#issuecomment-521762658  

Any updates on this?  
  
I got no answers from the travis community - https://travis-ci.community/t/im-trying-to-use-opencl-but-clinfo-reports-0-platforms/2611

---
