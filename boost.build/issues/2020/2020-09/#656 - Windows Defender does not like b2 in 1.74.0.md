# #656 - Windows Defender does not like b2 in 1.74.0 [Open]

> Username: xkszltl  
> Created at: 2020-09-12 23:27:01 UTC  
> Updated at: 2021-05-29 17:22:22 UTC  
> Url: https://github.com/boostorg/build/issues/656  

OS: Win Server 2019 (from Azure VM)  
Branch: 1.74.0, freshly cloned as part of the build script.  
Build command: https://github.com/xkszltl/Roaster/blob/361a80ab7ac5f37c89b2ec31fa1845ccdc70e74a/win/pkgs/boost.ps1#L47  
  
Build is stably killed by Windows Defender for `Behavior:Win32/Execution.LR!ml`.  
![image](https://user-images.githubusercontent.com/5203025/93006678-eb011080-f591-11ea-897d-3c5ccbc8336c.png)

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-09-13 13:58:50 UTC  
> Url: https://github.com/boostorg/build/issues/656#issuecomment-691674993  

I'm aware of the problem that Windows Defender is being onerous in it's checking.

---

## Comment 2

> Username: grafikrobot  
> Created at: 2020-09-13 14:00:53 UTC  
> Url: https://github.com/boostorg/build/issues/656#issuecomment-691675218  

For example... https://twitter.com/grafikrobot/status/1301882274850500608

---

## Comment 3

> Username: xkszltl  
> Created at: 2020-09-16 06:03:31 UTC  
> Url: https://github.com/boostorg/build/issues/656#issuecomment-693192548  

Actually, even with the working dir set as excluded from scanning, and windows defender's quarantined threats cleared, it can still stuck here sometimes, which make me think if there's a bug causing strange behavior and triggers AV.  
<img width="988" alt="image" src="https://user-images.githubusercontent.com/5203025/93298048-1baca880-f825-11ea-84d6-e10939d8e780.png">

---

## Comment 4

> Username: xkszltl  
> Created at: 2020-09-16 06:04:35 UTC  
> Url: https://github.com/boostorg/build/issues/656#issuecomment-693193035  

(or maybe Windows defender somehow decide to scan it regradless)

---

## Comment 5

> Username: xkszltl  
> Created at: 2020-09-16 06:48:20 UTC  
> Url: https://github.com/boostorg/build/issues/656#issuecomment-693209584  

Yes....Windows defender still kicks in....  
```  
PS C:\Users\tolia\Projects\roaster\win> .\setup.ps1 boost  
Re-entry "C:\Users\tolia\Projects\roaster\win\setup.ps1" with args "boost" for protection.  
rm : There is a mismatch between the tag specified in the request and the tag present in the reparse point  
At C:\Users\tolia\Projects\roaster\win\pkgs\env\scratch.ps1:9 char:1  
+ rm -Force -Recurse -ErrorAction SilentlyContinue -WarningAction Silen ...  
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
    + CategoryInfo          : NotSpecified: (:) [Remove-Item], Win32Exception  
    + FullyQualifiedErrorId : System.ComponentModel.Win32Exception,Microsoft.PowerShell.Commands.RemoveItemCommand  
```

---

## Comment 6

> Username: stale[bot]  
> Created at: 2021-05-29 17:21:52 UTC  
> Url: https://github.com/boostorg/build/issues/656#issuecomment-850868221  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
