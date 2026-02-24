# #128 Fix element_wise parameter ordering in element_sub call [Open]

> Username: isaac868  
> Created at: 2021-08-17 23:26:47 UTC  
> Updated at: 2021-09-09 12:45:29 UTC  
> Url: https://github.com/boostorg/ublas/pull/128  

Changed element_wise call within element_sub to match the correct parameter ordering. element_wise parameter ordering is a,b,result,op,queue, not a,b,op,result,queue as it is currently.  
#129

---
