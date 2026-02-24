# #94 - missing inline statement in exception.hpp [Closed]

> Username: ulle-em  
> Created at: 2020-11-18 10:21:35 UTC  
> Updated at: 2020-12-15 19:18:46 UTC  
> Closed at: 2020-12-15 19:18:46 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/94  

Function `const char * literal_string(const safe_numerics_error & e)` defined in exception.hpp, line 48 should be inline. Otherwise compilation fails due to multiply defined symbol found when using using safe numerics in two compilation units being linked together.

---

## Comment 1

> Username: robertramey  
> Created at: 2020-11-18 16:41:29 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/94#issuecomment-729802677  

Hmmm - interesting.  It seems to me that this might be a general issue for which you have stumbled on a specific example.  I wrote inline code all over the place as it's a header only library.  I didn't think about this issue.  So I would guess that there are lots of places where such an adjustment should be made.  Would you care to invest some effort to investigate this in more depth?  Also it would be interesting to some of with some sort of test which would detect all these cases.  It may well be compiler specific - what compiler are you using.  I could easily envisage that all inline definitions might be implicitly inline.  Or that this inlining might be influenced by some compiler switch.  Or that the linker might be smart enough to detect and consolidate duplicated inline definitions.  But then this might raise issues with shared libraries.  Perhaps the best solution would be to make such functions "private" either by moving them to a different module or perhaps by manipulation of "visibility" key words - usage of which of course vary according the particular compiler being used.  
  
Welcome to the world of portable library design and implementation.  
  
Thanks for spotting this,  I'll look into it.  As I said above you might want to do the same.

---

## Comment 2

> Username: GregKon  
> Created at: 2020-11-28 16:57:24 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/94#issuecomment-735255864  

I does confirm this issue. I just merge safe_numeric from master.  
It is actually linker issue not compiling issue. Your test can not discover this problem because in test you always use single compilation unit single file. If you create test with more than single source file than you may catch this error.  
In additional, I do not use GCC or MSVC (what are very "relaxed"), but very strict ISO compiler, so this also may play role.  
  
Merging from Master will break my policy, because you change way to call exception:  
from:  
        AE(e, msg);  
to:  
        AE()(e, msg);  
  
Not big issue, however it does stop compiling and break backward compatibility.

---

## Comment 3

> Username: voivoid  
> Created at: 2020-12-12 10:22:10 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/94#issuecomment-743735062  

I observe the same issue.  
  
The problems is pretty similar to https://github.com/boostorg/safe_numerics/issues/71 ( that was fixed a while ago )

---

## Comment 4

> Username: robertramey  
> Created at: 2020-12-15 19:18:42 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/94#issuecomment-745508875  

I've uploaded changes to make all freestanding functions defined in a header file "inline".  This issue raises all sorts of interesting  questions as to what happens when functions are defined in headers with regards to shared libraries, static libraries, API etc.  This is actually a question for all "header only" libraries.  But it will end up as a real research project and sales campaign and I'm bogged down with other stuff here.
