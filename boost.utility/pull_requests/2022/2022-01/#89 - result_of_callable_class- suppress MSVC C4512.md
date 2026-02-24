# #89 result_of_callable_class: suppress MSVC C4512 [Merged]

> Username: Kojoley  
> Created at: 2022-01-16 19:34:44 UTC  
> Updated at: 2022-01-17 15:31:08 UTC  
> Merged at: 2022-01-17 14:29:33 UTC  
> Closed at: 2022-01-17 14:29:33 UTC  
> Url: https://github.com/boostorg/utility/pull/89  

`result_of_callable_class` derives from a user provided type which might not be eligible for an implicit assignment operator causing an annoying warning

---

## Comment 1

> Username: Lastique  
> Created_at: 2022-01-16 20:12:32 UTC  
> Url: https://github.com/boostorg/utility/pull/89#issuecomment-1013944066  

Maybe merge it with the other warning suppression above, in line 124? That one is missing push/pop pragmas, which probably needs to be fixed.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2022-01-16 20:18:23 UTC  
> Url: https://github.com/boostorg/utility/pull/89#issuecomment-1013945000  

4913 suppression should be done below where the actual warning is emitted, I can fix that in this PR if you want, but I retracted from touching it because that might cause havoc downstream.

---

## Comment 3

> Username: Lastique  
> Created_at: 2022-01-16 20:31:17 UTC  
> Url: https://github.com/boostorg/utility/pull/89#issuecomment-1013947072  

I was thinking about just disabling both warnings for the whole file, including the Boost.PP-generated part. I.e. one `push` with `disable` for both warnings at the top, and one `pop` at the end. IMHO, that would make the code cleaner, with less pragmas scattered around.  
  
However, if you prefer to keep the pragmas more localized, I'm fine with that. 4913 needs to be fixed though: `#if` -> `#ifdef`, add another pair of `push`/`pop`.

---

## Comment 4

> Username: Lastique  
> Created_at: 2022-01-17 14:51:45 UTC  
> Url: https://github.com/boostorg/utility/pull/89#issuecomment-1014624250  

I've found [ticket 7663](https://svn.boost.org/trac10/ticket/7663) which explains that 4913 is flagged at the point of instantiation, so the warning has to be disabled globally. I've added the link in the comment.  
  
Thanks for the patch.

---

## Comment 5

> Username: Kojoley  
> Created_at: 2022-01-17 15:31:05 UTC  
> Url: https://github.com/boostorg/utility/pull/89#issuecomment-1014665546  

> I've found [ticket 7663](https://svn.boost.org/trac10/ticket/7663) which explains that 4913 is flagged at the point of instantiation, so the warning has to be disabled globally. I've added the link in the comment.  
>   
> Thanks for the patch.  
  
It could be that the issue was not actually in result_of, or recent compiler versions have better control of the warning, but I cannot check that myself atm.  
  
Thanks for the merge.

---
