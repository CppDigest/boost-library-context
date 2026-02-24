# #171 Trim trailing whitespaces in all source code files [Merged]

> Username: mloskot  
> Created at: 2018-12-04 20:59:51 UTC  
> Updated at: 2018-12-05 07:52:22 UTC  
> Merged at: 2018-12-05 07:51:34 UTC  
> Closed at: 2018-12-05 07:51:34 UTC  
> Url: https://github.com/boostorg/gil/pull/171  

PowerShell script used to perform the trimming:  
  
```powershell  
Get-ChildItem -Recurse -Include @("*.cpp", "*.hpp") |  
  ForEach-Object { (Get-Content $_.FullName)  
      | Foreach {$_.TrimEnd()} | Set-Content $_.FullName }  
```  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---
