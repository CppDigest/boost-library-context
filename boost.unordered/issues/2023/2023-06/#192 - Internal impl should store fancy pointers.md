# #192 - Internal impl should store fancy pointers [Closed]

> Username: cmazakas  
> Created at: 2023-06-19 21:13:10 UTC  
> Updated at: 2023-09-28 20:34:01 UTC  
> Closed at: 2023-09-28 20:34:01 UTC  
> Url: https://github.com/boostorg/unordered/issues/192  

This enables the flat/node containers to support being placed in shared memory and being used across processes.  
  
This would fix https://github.com/boostorg/unordered/issues/189

---

## Comment 1

> Username: cmazakas  
> Created at: 2023-09-28 20:34:01 UTC  
> Url: https://github.com/boostorg/unordered/issues/192#issuecomment-1739966727  

Fixed by #211
