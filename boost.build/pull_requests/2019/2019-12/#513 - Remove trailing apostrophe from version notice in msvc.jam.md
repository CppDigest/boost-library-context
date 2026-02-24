# #513 Remove trailing apostrophe from version notice in msvc.jam [Merged]

> Username: mloskot  
> Created at: 2019-12-16 20:16:27 UTC  
> Updated at: 2021-10-02 21:13:40 UTC  
> Merged at: 2019-12-16 20:56:04 UTC  
> Closed at: 2019-12-16 20:56:04 UTC  
> Url: https://github.com/boostorg/build/pull/513  

Fixes formatting of these notices  
  
```  
notice: will use 'C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.24.28314\bin\Hostx64\x64\cl.exe' for msvc, condition <toolset>msvc-14.2  
'notice: [generate-setup-cmd] 14.24.28314 is 14.2'  
'notice: [generate-setup-cmd] 14.24.28314 is 14.2'  
'notice: [generate-setup-cmd] 14.24.28314 is 14.2'  
'notice: [generate-setup-cmd] 14.24.28314 is 14.2'  
'notice: [generate-setup-cmd] 14.24.28314 is 14.2'  
notice: [msvc-cfg] condition: '<toolset>msvc-14.2/<architecture>/<address-model>', setup: 'C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Auxiliary\Build\vcvars32.bat'  
```

---
