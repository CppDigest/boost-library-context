# #694 - Temp variable uses wrong storage [Open]

> Username: vinniefalco  
> Created at: 2022-04-15 16:05:40 UTC  
> Updated at: 2022-05-19 17:55:19 UTC  
> Url: https://github.com/boostorg/json/issues/694  

I think instead of using the array's storage it should use the default storage:  
https://github.com/boostorg/json/blob/83c364afaf31b58cfe8b97ab159c406c508f47c9/include/boost/json/impl/array.hpp#L527  
  
Otherwise we are allocating and deallocating in the wrong order, and we are not making correct use of monotonic storage. We should review all functions which require temporary storage (e.g. `object::insert`).

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-05-15 10:10:05 UTC  
> Url: https://github.com/boostorg/json/issues/694#issuecomment-1126901072  

Can you explain the issue? I'm not seeing any issues by looking at the code. I also tried creating a test case and checking it with several sanitizers, but nothing manifested.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-05-15 16:47:18 UTC  
> Url: https://github.com/boostorg/json/issues/694#issuecomment-1126976943  

The sanitizer isn't going to tell you that we are using the monotonic storage in an inefficient way.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-05-15 16:47:40 UTC  
> Url: https://github.com/boostorg/json/issues/694#issuecomment-1126977010  

> Can you explain the issue?  
  
Yes:  
  
> Otherwise we are allocating and deallocating in the wrong order,

---

## Comment 4

> Username: grisumbras  
> Created at: 2022-05-16 21:35:37 UTC  
> Url: https://github.com/boostorg/json/issues/694#issuecomment-1128161383  

Can you elaborate what's wrong in that order?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-05-16 23:36:14 UTC  
> Updated at: 2022-05-16 23:36:27 UTC  
> Url: https://github.com/boostorg/json/issues/694#issuecomment-1128241069  

When you have monotonic storage, you want to deallocate in reverse order. The problem with this code is that it allocates from the container's storage to create the temporary range, it inserts the temporary range into the container (which can cause a realloc) and then it destroys the temporary range. This is a diagram:  
  
The container's current allocation A:  
```  
| A |  
```  
  
Now we allocate a temporary buffer B for the range:  
```  
| A | B |  
```  
  
Now we allocate, copy, and free. The container's new buffer is C. Note how there is an empty space where A was, because A is freed.  
  
```  
|   | B | C |  
```  
  
Now we deallocate the temporary buffer B:  
  
```  
|   |   | C |  
```  
  
We should instead use the global heap to allocate B, for two reasons:  
  
1. because it will be guaranteed to be freed before the function returns, and  
2. because it doesn't waste memory if the memory resource's deallocate is a no-op  
  
I hope this explains it.

---

## Comment 6

> Username: grisumbras  
> Created at: 2022-05-19 17:43:41 UTC  
> Url: https://github.com/boostorg/json/issues/694#issuecomment-1132005375  

Ah, so it's not a correctness issue, it's a resource usage issue.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2022-05-19 17:55:19 UTC  
> Url: https://github.com/boostorg/json/issues/694#issuecomment-1132015505  

That's right
