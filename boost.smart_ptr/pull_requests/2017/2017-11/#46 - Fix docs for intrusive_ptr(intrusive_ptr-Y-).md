# #46 Fix docs for intrusive_ptr(intrusive_ptr<Y>) [Closed]

> Username: matwey  
> Created at: 2017-11-28 18:01:36 UTC  
> Updated at: 2017-11-28 18:12:35 UTC  
> Closed at: 2017-11-28 18:11:09 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/46  

decltype(r.get()) is Y* when constructing new intrusive_ptr<T> instance from  
intrusive_ptr<Y> r. So, one may think that intrusive_ptr_add_ref(Y*) is called.  
  
It is important that intrusive_ptr_add_ref(T*) and intrusive_ptr_add_ref(Y*)  
may have different implementations.  
  
Actual implementation assigns r.get() to member variable T* xp and then calls  
intrusive_ptr_add_ref(xp), so we have to explicitly specify that it is  
intrusive_ptr_add_ref(T*) what will be actually called.  
  
Signed-off-by: Matwey V. Kornilov <matwey.kornilov@gmail.com>

---

## Comment 1

> Username: pdimov  
> Created_at: 2017-11-28 18:11:09 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/46#issuecomment-347612975  

But it most definitely doesn't `reinterpret_cast`. :-)  
  
Fixed in https://github.com/boostorg/smart_ptr/commit/2553a71b7922f3f8dff6472498459005c90e567c.

---

## Comment 2

> Username: matwey  
> Created_at: 2017-11-28 18:12:34 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/46#issuecomment-347613361  

Ok, thanks.  
  
2017-11-28 21:11 GMT+03:00 Peter Dimov <notifications@github.com>:  
  
> But it most definitely doesn't reinterpret_cast. :-)  
>  
> Fixed in 2553a71  
> <https://github.com/boostorg/smart_ptr/commit/2553a71b7922f3f8dff6472498459005c90e567c>  
> .  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/smart_ptr/pull/46#issuecomment-347612975>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AAJj0154axu52Bp9c6k6InWse1NxsB5Hks5s7Ey-gaJpZM4Qtqkc>  
> .  
>  
  
  
  
--   
With best regards,  
Matwey V. Kornilov

---
