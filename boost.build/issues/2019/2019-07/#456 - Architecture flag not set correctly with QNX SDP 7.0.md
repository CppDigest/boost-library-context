# #456 - [bug] Architecture flag not set correctly with QNX SDP 7.0 [Open]

> Username: craigsacco  
> Created at: 2019-07-09 00:23:04 UTC  
> Updated at: 2021-05-29 17:22:42 UTC  
> Url: https://github.com/boostorg/build/issues/456  

I was tackling an issue attempting to compile Boost 1.66.0 through QNX SDP 7.0 to target the x86_64 target. I discovered that the **-V** switch wasn't being inserted into the q++ command line when different architecture or address model settings are passed into B2.  
  
A similar issue was raised last year (issue #339) but the solution is not satisfactory as simply passing the **-V** flag through the **compileflags** and **linkflags** properties will be ignored when creating static libraries.  
  
I ended up retrofitting [**tools/qcc.jam**](https://github.com/boostorg/build/blob/master/src/tools/qcc.jam) with the following changes to make it work correctly.  
  
**NOTE:** line numbers applying to the current version of the file, not the version in 1.66.0.  
  
Below line 80:  
  
```  
toolset.flags qcc.compile OPTIONS <architecture>x86/<address-model>32 : -Vgcc_ntox86 ;  
toolset.flags qcc.compile OPTIONS <architecture>x86/<address-model>64 : -Vgcc_ntox86_64 ;  
toolset.flags qcc.compile OPTIONS <architecture>arm/<address-model>32 : -Vgcc_ntoarmv7le ;  
toolset.flags qcc.compile OPTIONS <architecture>arm/<address-model>64 : -Vgcc_ntoaarch64le ;  
```  
  
Below line 173:  
  
```  
toolset.flags qcc.link OPTIONS <architecture>x86/<address-model>32 : -Vgcc_ntox86 ;  
toolset.flags qcc.link OPTIONS <architecture>x86/<address-model>64 : -Vgcc_ntox86_64 ;  
toolset.flags qcc.link OPTIONS <architecture>arm/<address-model>32 : -Vgcc_ntoarmv7le ;  
toolset.flags qcc.link OPTIONS <architecture>arm/<address-model>64 : -Vgcc_ntoaarch64le ;  
```  
  
Below line 212:  
  
```  
toolset.flags qcc.archive OPTIONS <architecture>x86/<address-model>32 : -Vgcc_ntox86 ;  
toolset.flags qcc.archive OPTIONS <architecture>x86/<address-model>64 : -Vgcc_ntox86_64 ;  
toolset.flags qcc.archive OPTIONS <architecture>arm/<address-model>32 : -Vgcc_ntoarmv7le ;  
toolset.flags qcc.archive OPTIONS <architecture>arm/<address-model>64 : -Vgcc_ntoaarch64le ;  
```  
  
Modified the line in the **archive** action (line 226):  
  
From: `"$(CONFIG_COMMAND)" -A "$(<)" "$(>)"`  
To: `"$(CONFIG_COMMAND)" $(OPTIONS) -A "$(<)" "$(>)"`  
  
The last change is the most important as it was inadvertently calling the 32-bit version of **ar** to archive object files compiled through the 64-bit version of **gcc**.

---

## Comment 1

> Username: mjungnickel18  
> Created at: 2020-05-12 05:25:06 UTC  
> Url: https://github.com/boostorg/build/issues/456#issuecomment-627116522  

At least for my try to get Boost 1.73 to compile for QNX 7 I had to add  
  
toolset.flags qcc.compile++ OPTIONS <architecture>x86/<address-model>32 : -Vgcc_ntox86 ;  
toolset.flags qcc.compile++ OPTIONS <architecture>x86/<address-model>64 : -Vgcc_ntox86_64 ;  
toolset.flags qcc.compile++ OPTIONS <architecture>arm/<address-model>32 : -Vgcc_ntoarmv7le ;  
toolset.flags qcc.compile++ OPTIONS <architecture>arm/<address-model>64 : -Vgcc_ntoaarch64le ;  
  
As well to compile C and C++ code for the correct architecture.  
And of course add the fix of #453

---

## Comment 2

> Username: stale[bot]  
> Created at: 2021-05-29 17:22:12 UTC  
> Url: https://github.com/boostorg/build/issues/456#issuecomment-850868294  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
