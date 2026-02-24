# #30 - Custom fiber properties and scheduling [Closed]

> Username: erakadjiev  
> Created at: 2015-02-21 12:44:53 UTC  
> Updated at: 2015-07-06 20:05:27 UTC  
> Closed at: 2015-07-06 20:05:27 UTC  
> Url: https://github.com/boostorg/fiber/issues/30  

Hi Oliver,  
  
I have different types of fibers, and I'd need a scheduler that makes scheduling decisions based on the fiber type (or any custom property of the fiber). I could approximate what I want with fiber priorities, which used to exist in the `master` branch.  
  
Is there any good way to do this in the `develop` branch? I haven't found one.  
  
A derived class of `fiber` doesn't help, because the scheduler works with `fiber_context`.  
Creating derived classes of both `fiber` and `fiber_context` could work, but currently it doesn't, due to two issues: in `fiber`, the wrapped `fiber_context` is hidden as a private member, and `fiber` is not polymorphic, its methods aren't virtual. So even if I would reimplement all methods of `fiber` in my derived fiber class in order to use my derived fiber_context, it would not work.  
  
Having a way to do this type of customization would be very useful, probably for many people.   
The easiest solution would be to change the the private section of `fiber` to protected. However, Asio `spawn()` would have to be modified to support the custom fiber types, too.  
  
Thanks!

---

## Comment 1

> Username: olk  
> Created at: 2015-07-06 20:05:27 UTC  
> Url: https://github.com/boostorg/fiber/issues/30#issuecomment-118980175  

custom properties scheduler added
