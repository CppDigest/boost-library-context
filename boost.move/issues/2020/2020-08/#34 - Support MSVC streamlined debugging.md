# #34 - Support MSVC streamlined debugging [Closed]

> Username: vinniefalco  
> Created at: 2020-08-29 16:28:42 UTC  
> Updated at: 2020-09-03 17:41:35 UTC  
> Closed at: 2020-09-01 12:48:11 UTC  
> Url: https://github.com/boostorg/move/issues/34  

MSVC has a way to annotate utility functions like `boost::forward` to tell the debugger it is not necessary to step into it. This would make debugging a lot nicer in the VS IDE.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-09-01 09:39:21 UTC  
> Url: https://github.com/boostorg/move/issues/34#issuecomment-684671324  

Which annotation is that? I can't find anything official. Something like "[DebuggerNonUserCode()]" for C#?. Any example of this kind of annotation?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-09-01 09:55:33 UTC  
> Url: https://github.com/boostorg/move/issues/34#issuecomment-684695163  

It is called 'Just My Code' and I believe you do it by making an XML file ending in ".natjmc" and putting the stuff in it. Kind of like the .natvis file:  
https://docs.microsoft.com/en-us/visualstudio/debugger/just-my-code?view=vs-2019

---

## Comment 3

> Username: igaztanaga  
> Created at: 2020-09-01 12:48:11 UTC  
> Url: https://github.com/boostorg/move/issues/34#issuecomment-684827362  

Then I think this should be handled at a general boost level instead of library level or at least establish guidelines between libraries, as for some users a function should be skippable but not for others.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-09-01 12:54:27 UTC  
> Url: https://github.com/boostorg/move/issues/34#issuecomment-684830847  

I disagree. Boost reimplements many functions from the standard. Or rather, I should say that the standard reimplements many functions from Boost but that's a different conversation :) Take for example `std::forward`. In MSVC this is skipped over - no one needs to see this. I don't think anyone needs to see the Boost version of it either. Same for `std::move`. I think that if MSVC skips a stdlib function, then it is reasonable for Boost to do the same thing.  
  
There does not need to be guidelines between libraries for this and it doesn't need to be handled at any higher level than the particular library which reimplements std functions. Users can always turn off Just My Code. But in all the time I have been using VS with this feature I have never wanted to do that.  
  
The key observation here is that there are functions which no one wants or needs to step into, which are completely non-controversial to configure to be skipped in the IDE, and that the Boost equivalents should also be skipped. These functions are purely noise with zero informational value, they just get in the way of debugging. This is why Microsoft added the Just My Code feature to MSVC.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-09-03 17:41:35 UTC  
> Url: https://github.com/boostorg/move/issues/34#issuecomment-686646856  

Here is another example:  
https://github.com/boostorg/container/blob/3da7877dc005d158b90a72fff697bf2905763ee8/include/boost/container/pmr/memory_resource.hpp#L44  
  
Stepping into this should step directoy into the derived class' `do_allocate`, et. al., and skip the base class (the `memory_resource::allocate` member function. There is absolutely zero benefit to seeing this boilerplate. MSVC does this for its standard library `memory_resource`.
