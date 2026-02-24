# #70 - A single-header version? [Closed]

> Username: akrzemi1  
> Created at: 2024-01-16 21:50:17 UTC  
> Updated at: 2024-01-18 07:00:33 UTC  
> Closed at: 2024-01-18 07:00:33 UTC  
> Url: https://github.com/boostorg/parser/issues/70  

Would it be possible to also ship a single-header version of this library?  
Such header-only variants make it so easy to test and demo such library in Compiler Explorer. Especially useful in a work envireonment when one is not allowed to download or use or install practically anything.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2024-01-16 22:56:09 UTC  
> Url: https://github.com/boostorg/parser/issues/70#issuecomment-1894652526  

Ok, I found a tool to do it myself: https://pypi.org/project/pcpp  
Now I can test the library with Compiler Explorer: https://godbolt.org/z/Y4fEWhrbo  
Still, you might consider providing such a "combined header" for easy online testing.

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-01-17 05:36:43 UTC  
> Url: https://github.com/boostorg/parser/issues/70#issuecomment-1894973337  

Interesting!  I think I'll try to set this up as a Github action, pushing to a branch.  I think this would be very nice to have if its creation could be automated.
