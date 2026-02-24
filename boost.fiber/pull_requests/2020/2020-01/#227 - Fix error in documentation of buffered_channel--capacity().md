# #227 Fix error in documentation of buffered_channel::capacity(). [Closed]

> Username: ingomueller-net  
> Created at: 2020-01-05 18:59:20 UTC  
> Updated at: 2020-01-06 11:28:04 UTC  
> Closed at: 2020-01-06 11:28:04 UTC  
> Url: https://github.com/boostorg/fiber/pull/227  

The capacity bust be *at least 2*, not *at most 2*, as the documentation currently wrongly says.

---

## Comment 1

> Username: olk  
> Created_at: 2020-01-05 21:07:25 UTC  
> Url: https://github.com/boostorg/fiber/pull/227#issuecomment-570947766  

the doc is correct - the ctor will throw fiber_error if `2 > capacity_ || 0 != ( capacity_ & (capacity_ - 1))` does evaluate to true

---

## Comment 2

> Username: ingomueller-net  
> Created_at: 2020-01-06 09:01:08 UTC  
> Url: https://github.com/boostorg/fiber/pull/227#issuecomment-571059173  

OK, true, the code *does* say that. But either I don't understand something, or we have an even bigger problem: As it seems, the current `buffered_channel` can **only** be constructed with `capacity == 2`, no? (It's a `size_t`, so there are only the values 0, 1, and 2 that satisfy `capacity <= 2`, and 0 and 1 do not satisfy `0!=(capacity & (capacity-1)`.) Is this really intentional?

---

## Comment 3

> Username: olk  
> Created_at: 2020-01-06 09:32:21 UTC  
> Url: https://github.com/boostorg/fiber/pull/227#issuecomment-571068901  

precodition is `capacity >= 2` && `0 == ( capacity & (capacity - 1)` - this means capacity must be a power of two

---

## Comment 4

> Username: ingomueller-net  
> Created_at: 2020-01-06 11:28:04 UTC  
> Url: https://github.com/boostorg/fiber/pull/227#issuecomment-571105477  

So sorry! I misread `2<=capacity` as `capacity<=2` repeatedly...

---
