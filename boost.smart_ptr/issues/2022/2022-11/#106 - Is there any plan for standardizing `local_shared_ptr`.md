# #106 - Is there any plan for standardizing `local_shared_ptr`? [Open]

> Username: frederick-vs-ja  
> Created at: 2022-11-29 17:26:08 UTC  
> Updated at: 2022-11-29 18:44:34 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/106  

I'm sorry for bothering here. This is not an issue for **`boost::`**`local_shared_ptr`.  
  
Arthur O'Dwyer said that `local_shared_ptr` has been rejected for standardization (on [std-proposals](https://lists.isocpp.org/std-proposals/2020/07/1569.php)). But it seems that there's not even a P-paper for it.  
  
I wonder whether the standardization is really rejected, and if not, whether there is any plan for it.

---

## Comment 1

> Username: pdimov  
> Created at: 2022-11-29 17:56:33 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/106#issuecomment-1331070100  

I'm not aware of any attempts to propose `local_shared_ptr` for standardization. I'm not sure what Arthur was referring to. @Quuxplusone?

---

## Comment 2

> Username: Quuxplusone  
> Created at: 2022-11-29 18:25:05 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/106#issuecomment-1331106983  

@pdimov: You think I remember what I was thinking in a parenthetical from two years ago? ;)  Searching for "non-thread-safe shared_ptr" turns up some old discussions like  
- https://groups.google.com/a/isocpp.org/g/std-proposals/c/LNaFSlj_Y-0 (2015, Ville seems amenable)  
- https://github.com/WG21-SG14/SG14/issues/59 (2016, @onqtam considered making a formal paper but didn't)  
- https://stackoverflow.com/questions/6593770/creating-a-non-thread-safe-shared-ptr (no WG21-related discussion)  
  
I might have been hazily remembering the 2015 and 2016 discussions, which were _WG21-adjacent_ and didn't result in anything being adopted, but also weren't formal proposals either.

---

## Comment 3

> Username: pdimov  
> Created at: 2022-11-29 18:44:34 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/106#issuecomment-1331128947  

Reading that SO link reminds me that I need to find the time to implement local_weak_ptr one of these days.
