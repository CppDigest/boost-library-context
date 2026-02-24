# #68 Simplify `value_initialized` definition [Closed]

> Username: Kojoley  
> Created at: 2020-06-08 21:21:11 UTC  
> Updated at: 2021-04-23 21:12:50 UTC  
> Closed at: 2020-06-08 22:40:21 UTC  
> Url: https://github.com/boostorg/utility/pull/68  



---

## Comment 1

> Username: Lastique  
> Created_at: 2020-06-08 22:14:20 UTC  
> Url: https://github.com/boostorg/utility/pull/68#issuecomment-640915447  

Is there a reason why we have two templates for this? Besides backward compatibility.

---

## Comment 2

> Username: Lastique  
> Created_at: 2020-06-08 22:19:04 UTC  
> Url: https://github.com/boostorg/utility/pull/68#issuecomment-640917101  

@Kojoley Please, take a look at test failures.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2020-06-08 22:27:10 UTC  
> Url: https://github.com/boostorg/utility/pull/68#issuecomment-640920389  

I do not know, the `initialized` appeared later, the difference seems to be only in forwarding constructor. The only usage in the whole Boost of those facilities is MPL.  
  
The compile fail in GCC 10 seems strange, there is no error message. The clang fails seems like unimplemented DR253, do we have a macro for it?

---

## Comment 4

> Username: Lastique  
> Created_at: 2020-06-08 22:36:46 UTC  
> Url: https://github.com/boostorg/utility/pull/68#issuecomment-640923515  

> The clang fails seems like unimplemented DR253, do we have a macro for it?  
  
I'm not aware of one.

---

## Comment 5

> Username: Kojoley  
> Created_at: 2020-06-08 22:40:21 UTC  
> Url: https://github.com/boostorg/utility/pull/68#issuecomment-640924642  

Well, DR253 is not resolved, I learned something new today.  
  
At this point I do not see much value in the patch, sorry for the noise.

---
