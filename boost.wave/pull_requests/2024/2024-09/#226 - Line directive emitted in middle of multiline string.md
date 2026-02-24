# #226 Line directive emitted in middle of multiline string [Closed]

> Username: njnobles  
> Created at: 2024-09-25 21:11:47 UTC  
> Updated at: 2024-10-02 18:29:19 UTC  
> Closed at: 2024-10-02 18:29:19 UTC  
> Url: https://github.com/boostorg/wave/pull/226  

Attempts to address https://github.com/boostorg/wave/issues/225  
  
PR reverts the changes to cpp_iterator.hpp that were introduced in https://github.com/boostorg/wave/pull/140

---

## Comment 1

> Username: jefftrull  
> Created_at: 2024-09-26 03:48:10 UTC  
> Url: https://github.com/boostorg/wave/pull/226#issuecomment-2375807936  

Sorry I misunderstood the test situation here, you already thoughtfully provided one as you did for #223!  
  
I'll try to come up with my own fix that doesn't completely revert #140 since I like the refactoring part of it. #170 is the redundant commit (w.r.t this bug).

---

## Comment 2

> Username: jefftrull  
> Created_at: 2024-10-02 18:29:19 UTC  
> Url: https://github.com/boostorg/wave/pull/226#issuecomment-2389421444  

Thanks for all the work you've done on this. It's far more detail than we usually get for bugs, and I appreciate it.  
We're going to close this in favor of improving the lexer error checking, rejecting these kinds of constructs entirely.

---
