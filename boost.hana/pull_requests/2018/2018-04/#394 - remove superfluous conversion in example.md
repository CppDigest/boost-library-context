# #394 [tuple] remove superfluous conversion in example [Merged]

> Username: ricejasonf  
> Created at: 2018-04-30 16:09:13 UTC  
> Updated at: 2018-05-06 16:13:04 UTC  
> Merged at: 2018-05-06 16:13:04 UTC  
> Closed at: 2018-05-06 16:13:04 UTC  
> Url: https://github.com/boostorg/hana/pull/394  

The documentation states that the result of `tuple_c` is functionally equivalent to the call to `make_tuple` so the example should not require a conversion. Correct me if I am wrong.  
  
I've seen confusion about this before, and here is the question that prompted this:  
https://stackoverflow.com/questions/50101953/why-is-the-type-of-boosthanatuple-c-implementation-defined  
  
Thanks :smile:

---

## Comment 1

> Username: ldionne  
> Created_at: 2018-05-06 16:10:34 UTC  
> Url: https://github.com/boostorg/hana/pull/394#issuecomment-386890418  

You are right. Not sure why there's a conversion in the first place.

---
