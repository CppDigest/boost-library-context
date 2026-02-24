# #466 - Please add reserve() to wait_list [Closed]

> Username: piotrwyderski  
> Created at: 2015-06-18 08:18:35 UTC  
> Updated at: 2016-04-30 03:16:39 UTC  
> Closed at: 2016-04-30 03:16:36 UTC  
> Url: https://github.com/boostorg/compute/issues/466  

wait_list is internally based on std::vector (the m_events field),  
but there is no way to request a memory block reservation even  
if the size is known, which leads to unnecessary reallocations.  
Please add the reserve() method which would simply forward its  
argument(s -- a variadic template-based forwarder perhaps?) to  
m_events.

---

## Comment 1

> Username: kylelutz  
> Created at: 2015-06-19 02:42:15 UTC  
> Url: https://github.com/boostorg/compute/issues/466#issuecomment-113348509  

Good idea, this shouldn't be too difficult to do.

---

## Comment 2

> Username: kylelutz  
> Created at: 2016-04-30 03:16:35 UTC  
> Url: https://github.com/boostorg/compute/issues/466#issuecomment-215928658  

Implemented in PR #574.
