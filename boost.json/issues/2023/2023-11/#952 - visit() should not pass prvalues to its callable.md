# #952 - visit() should not pass prvalues to its callable [Closed]

> Username: ned14  
> Created at: 2023-11-10 17:58:56 UTC  
> Updated at: 2024-02-08 08:19:40 UTC  
> Closed at: 2024-02-08 08:19:40 UTC  
> Url: https://github.com/boostorg/json/issues/952  

Otherwise lvalue ref taking callables don't compile: https://godbolt.org/z/4j3efaP9W  
  
@pdimov Asked me to submit this bug.

---

## Comment 1

> Username: pdimov  
> Created at: 2023-11-10 18:08:54 UTC  
> Url: https://github.com/boostorg/json/issues/952#issuecomment-1806190832  

https://godbolt.org/z/5Pdd7nvbz doesn't work, one needs to do https://godbolt.org/z/8YocbodaG.  
  
The workaround is to use `auto&&`, but it would be more consistent for `auto&` to work, as `nullptr` is the only case that passes an rvalue.

---

## Comment 2

> Username: grisumbras  
> Created at: 2023-11-24 12:36:28 UTC  
> Url: https://github.com/boostorg/json/issues/952#issuecomment-1825617311  

I've written a fix, but then decided to also write a rvalue reference overload for visit, at which point I've reached a conundrum. We don't have rvalue ref-qualified overloads for e.g. `get_bool` and `get_double`, as there's no need for them. Due to that a visitor that has a bunch of `operator(T&&)` overloads will not work. Which makes me rethink whether it's actually necessary to make `visitor::operator()(nullptr_t&)` work. Maybe it is better to simply better explain the behaviour in documentation.  
  
On the other hand, a visitor with `operator(T)` overloads _will_ work with rvalue ref-qualified overload, so maybe that's good enough?
