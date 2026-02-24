# #566 - Absolute MSVC linker path in config has to be double quoted. [Open]

> Username: qis  
> Created at: 2020-04-09 04:39:42 UTC  
> Updated at: 2021-05-29 18:22:19 UTC  
> Url: https://github.com/boostorg/build/issues/566  

Currently I have to double quote the `<linker>` setting in a project config file, otherwise `msvc.archive` commands fail with the error message:  
  
```  
'C:/Program' is not recognized as an internal or external command  
```  
  
Start of the config used in the `b2` command line option `--project-config=config.jam`:  
  
```  
using msvc : 14.2 : "C:/Program Files (x86)/Microsoft Visual Studio/2019/Community/VC/Tools/MSVC/14.25.28610/bin/Hostx64/x64/cl.exe" :  
  
<linker>"\"C:/Program Files (x86)/Microsoft Visual Studio/2019/Community/VC/Tools/MSVC/14.25.28610/bin/Hostx64/x64/link.exe\""  
  
...  
```  
  
Is this intentional? Sometimes an absolute path is necessary, for example if the linker is replaced by a different version or with `lld-link.exe` which is not in `PATH`.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 18:21:49 UTC  
> Url: https://github.com/boostorg/build/issues/566#issuecomment-850877271  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
