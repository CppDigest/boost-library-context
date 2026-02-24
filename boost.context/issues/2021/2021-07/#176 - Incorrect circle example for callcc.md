# #176 - Incorrect circle example for callcc [Closed]

> Username: airstone42  
> Created at: 2021-07-05 15:07:35 UTC  
> Updated at: 2021-07-05 15:40:22 UTC  
> Closed at: 2021-07-05 15:40:22 UTC  
> Url: https://github.com/boostorg/context/issues/176  

https://github.com/boostorg/context/blob/eb7c04713220ed9d3b56d3ea7a5d562a8942568b/example/callcc/circle.cpp#L21  
  
It failed to compile as ```resume()``` was rvalue-ref qualified.  
  
https://github.com/boostorg/context/blob/eb7c04713220ed9d3b56d3ea7a5d562a8942568b/example/callcc/circle.cpp#L11  
  
It seems that this example was supposed to use ```callcc``` while it uses ```fiber``` now, which duplicates with [example/fiber/circle.cpp](https://github.com/boostorg/context/blob/develop/example/fiber/circle.cpp).

---

## Comment 1

> Username: olk  
> Created at: 2021-07-05 15:40:22 UTC  
> Url: https://github.com/boostorg/context/issues/176#issuecomment-874200557  

ty, fixed
