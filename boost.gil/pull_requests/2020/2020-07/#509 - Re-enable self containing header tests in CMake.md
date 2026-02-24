# #509 Re-enable self containing header tests in CMake [Merged]

> Username: sdebionne  
> Created at: 2020-07-16 12:19:21 UTC  
> Updated at: 2020-07-27 10:39:03 UTC  
> Merged at: 2020-07-26 19:06:33 UTC  
> Closed at: 2020-07-26 19:06:33 UTC  
> Url: https://github.com/boostorg/gil/pull/509  

I am not sure why they were disabledin the first place...  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2020-07-26 18:35:58 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/509#pullrequestreview-455386116  

To be honest, I can't clearly remember what was the reason I commented those tests.   
  
Good catch, @sdebionne, thanks!

---

## Comment 2

> Username: mloskot  
> Created_at: 2020-07-26 19:28:25 UTC  
> Url: https://github.com/boostorg/gil/pull/509#issuecomment-664030190  

Just for records, this change was tested on Azure Pipelines where we build with CMake  
https://dev.azure.com/boostorg/gil/_build/results?buildId=1047&view=results

---
