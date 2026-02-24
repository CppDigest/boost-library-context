# #238 - Wrong NumPy headers included when building boost.numpy with multiple configured Python versions [Open]

> Username: opalsTUW  
> Created at: 2018-10-30 18:39:53 UTC  
> Updated at: 2018-10-30 18:42:00 UTC  
> Url: https://github.com/boostorg/python/issues/238  

Having configured multiple Python-installations in python_config.jam, each like  
```  
using python  
    : 3.7 # version  
    : C:\\Python37-x64\\ # cmd-or-prefix  
    : C:\\Python37-x64\\include # includes  
    : C:\\Python37-x64\\libs # libraries  
    : <address-model>64 # condition  
    ;  
```  
The call to  
`bjam target-os=windows address-model=64 debug-symbols=on debug-store=database --user-config=python_config.jam --debug-configuration --debug-generator python=3.7 -d+2 --with-python stage`  
  
Shows in its output that bjam correctly detects for each of the configured Python installations if NumPy is available or not ("enabled"/"disabled").  
  
However, the output due to -d+2, shows that bjam includes the wrong NumPy headers. It seems that it includes them from the respective directory of whichever Python-installation comes last in python_config.jam and that has NumPy "enabled".  
  
Additionally, even if the debug-configuration-output says that NumPy is "disabled" for a certain Python-installation, bjam will still build boost.numpy - again, including the NumPy headers for whichever installation comes last in python_config.jam that has NumPy "enabled".  
  
Building boost 1.68.0 with VS2017 / 15.8.8 on Windows 10 x64 for Windows x64.
