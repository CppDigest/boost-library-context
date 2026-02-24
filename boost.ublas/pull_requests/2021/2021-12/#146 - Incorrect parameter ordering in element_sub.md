# #146 Incorrect parameter ordering in element_sub  [Open]

> Username: harjot20022001  
> Created at: 2021-12-29 16:51:55 UTC  
> Updated at: 2022-06-27 05:06:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/146  

The call in the first element_sub overload is changed to:  
element_wise(a, b, result, compute::minus<T>(), queue);  
as opposed to  
element_wise(a, b, compute::minus<T>(), result, queue);  
Fixes #129

---
