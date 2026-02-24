# #206 CallableType should be private [Closed]

> Username: shinobu-x  
> Created at: 2018-02-03 01:20:23 UTC  
> Updated at: 2018-02-04 08:29:14 UTC  
> Closed at: 2018-02-04 00:36:16 UTC  
> Url: https://github.com/boostorg/thread/pull/206  



---

## Comment 1

> Username: viboes  
> Created_at: 2018-02-03 08:23:29 UTC  
> Url: https://github.com/boostorg/thread/pull/206#issuecomment-362789946  

What is the added value? This is a detail implementation class.  
I believe that if the member `f` is public, his type should be public.

---

## Comment 2

> Username: viboes  
> Created_at: 2018-02-04 08:29:14 UTC  
> Url: https://github.com/boostorg/thread/pull/206#issuecomment-362889944  

I don't know where you have posted  
>Should this type also be public?  
>  
>https://github.com/shinobu-x/thread/blob/e60eca5212177386c271ce5046def824e5794123/include/boost/thread/future.hpp#L3176-L3182  
  
But the answer is yes.

---
