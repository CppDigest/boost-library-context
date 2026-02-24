# #713 - Allow implicit conversion from gray8_ptr_t to gray8_step_ptr_t [Closed]

> Username: huangqinjin  
> Created at: 2022-07-30 18:10:59 UTC  
> Updated at: 2022-08-01 14:54:17 UTC  
> Closed at: 2022-08-01 14:53:48 UTC  
> Url: https://github.com/boostorg/gil/issues/713  

In this case, the memunit step is just `sizeof(pixel)`.

---

## Comment 1

> Username: mloskot  
> Created at: 2022-07-30 22:53:50 UTC  
> Url: https://github.com/boostorg/gil/issues/713#issuecomment-1200308403  

It would be helpful if you could explain rationale behind your suggestion, what's the use case.

---

## Comment 2

> Username: huangqinjin  
> Created at: 2022-08-01 13:27:30 UTC  
> Url: https://github.com/boostorg/gil/issues/713#issuecomment-1201204719  

Such that `gray8_view_t` can convert to `gray8_step_view_t` implicitly.

---

## Comment 3

> Username: mloskot  
> Created at: 2022-08-01 14:53:48 UTC  
> Updated at: 2022-08-01 14:54:10 UTC  
> Url: https://github.com/boostorg/gil/issues/713#issuecomment-1201310810  

If there was one type of pixel/view/image available in GIL, such 'convenience' would make sense - wherever you see it missing - but it is a generic library...  
Unless, you present an actual problem or use case in form of a minimum working example, then it will be very hard to see what you really mean.
