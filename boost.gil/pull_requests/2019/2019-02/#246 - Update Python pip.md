# #246 [azp] Update Python pip [Merged]

> Username: mloskot  
> Created at: 2019-02-26 10:19:38 UTC  
> Updated at: 2019-02-26 20:02:46 UTC  
> Merged at: 2019-02-26 20:02:42 UTC  
> Closed at: 2019-02-26 20:02:42 UTC  
> Url: https://github.com/boostorg/gil/pull/246  

### Problem  
  
Attempt to solve recent build failures on Azure Pipelines as reported here, then pip update suggested as a solution:  
https://developercommunity.visualstudio.com/content/problem/469073/azp-script-is-installed-in-chostedtoolcachewindows.html  
  
### Solution  
  
Workaround for the problem was suggested in follow-up to the linked report. Adding this task to Windows jobs in `.azure-pipelines.yml` seems to solve the problem:  
  
```  
- task: UsePythonVersion@0  
    displayName: 'Setup Python'  
    inputs:  
      versionSpec: '3.6'   
      addToPath: true  
      architecture: 'x64'  
```  
  
### Tasklist  
  
- [x] All CI builds and checks have passed

---
