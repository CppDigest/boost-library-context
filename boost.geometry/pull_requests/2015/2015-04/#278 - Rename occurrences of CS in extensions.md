# #278 Rename occurrences of CS in extensions. [Merged]

> Username: awulkiew  
> Created at: 2015-04-02 01:26:46 UTC  
> Updated at: 2015-04-09 01:14:21 UTC  
> Merged at: 2015-04-05 00:26:27 UTC  
> Closed at: 2015-04-05 00:26:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/278  

This should fix several tests on Android and SunOS platforms where CS is a macro name.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-04-02 17:27:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/278#discussion_r27678227  

This is generated code.  
Anyway, I will update the generator (it is not yet in git)

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-04-02 17:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/278#issuecomment-88981152  

I'm OK, thanks

---

## Comment 3

> Username: awulkiew  
> Created_at: 2015-04-02 18:32:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/278#discussion_r27684182  

Ok, then I suggest to use lower-case letters for those names to avoid problems with macros and be in line with the Boost guidelines.  
  
Btw, where are those numbers from? They look like single-precision, probably not good enough for double or more precise type.

---
