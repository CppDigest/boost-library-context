# #165 - Fixing iteration-type of for-loop. [Open]

> Username: amitsingh19975  
> Created at: 2022-02-11 14:13:11 UTC  
> Updated at: 2022-03-11 15:07:49 UTC  
> Url: https://github.com/boostorg/ublas/issues/165  

The current loops rely on `auto` deduction or integer suffix for the type that may not be a correct iteration type and may produce conversion warnings all over places. Therefore, I propose to remove `auto` from the loops and use appropriate and explicit types or use explicit types to initialize the variable. In a long run, it would produce a bug-free code, and it would remove all warnings produced by compilers.  
  
From  
```cpp  
for(auto d = 0u; d < t.size(); ++d)  
{  
...  
}  
```  
  
To  
```cpp  
for(auto d = size_type{0}; d < t.size(); ++d)  
{  
...  
}  
```

---

## Comment 1

> Username: Neel-Shah-29  
> Created at: 2022-02-11 15:59:28 UTC  
> Updated at: 2022-02-11 16:00:25 UTC  
> Url: https://github.com/boostorg/ublas/issues/165#issuecomment-1036361632  

Can I take up this issue if no one is working upon it. Kindly assign it to me

---

## Comment 2

> Username: amitsingh19975  
> Created at: 2022-02-11 16:54:25 UTC  
> Updated at: 2022-02-11 17:00:50 UTC  
> Url: https://github.com/boostorg/ublas/issues/165#issuecomment-1036412289  

> Can I take up this issue if no one is working upon it. Kindly assign it to me  
  
I'm waiting for others' views on this. Therefore, wait for a few days. So, I request you to read the tensor's codebase and try to find bugs or enhancements that you think would improve the tensor. Finding bugs is also a way to contribute.

---

## Comment 3

> Username: Neel-Shah-29  
> Created at: 2022-02-19 08:28:37 UTC  
> Updated at: 2022-03-11 15:07:49 UTC  
> Url: https://github.com/boostorg/ublas/issues/165#issuecomment-1045967070  

Since 20 days, if no one is putting input over this issue , Can i take up this issue ?
