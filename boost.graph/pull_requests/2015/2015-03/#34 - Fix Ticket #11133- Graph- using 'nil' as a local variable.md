# #34 Fix Ticket #11133: Graph: using 'nil' as a local variable [Merged]

> Username: jaredgrubb  
> Created at: 2015-03-21 23:27:38 UTC  
> Updated at: 2016-04-22 18:14:16 UTC  
> Merged at: 2016-04-22 18:14:16 UTC  
> Closed at: 2016-04-22 18:14:16 UTC  
> Url: https://github.com/boostorg/graph/pull/34  

This is effectively a cosmetic change, as it only changes the name of a local variable. However, when this header gets in included in code compiled under ObjC++, the compiler doesnt like seeing 'nil' as a local variable (it's a keyword).  
  
I wasnt sure what name to pick, so I picked "nil_node". I'm happy to adjust it to something else.

---

## Comment 1

> Username: Belcourt  
> Created_at: 2015-05-15 01:40:07 UTC  
> Url: https://github.com/boostorg/graph/pull/34#issuecomment-102217999  

nil_node is okay, but I think it'd read better if it was changed to 'empty'.  Does that seem reasonable to you?

---

## Comment 2

> Username: jaredgrubb  
> Created_at: 2015-08-24 03:57:05 UTC  
> Url: https://github.com/boostorg/graph/pull/34#issuecomment-134015640  

I totally missed this comment. I have adjusted the variable name to "empty" and rebased the commit.

---

## Comment 3

> Username: jaredgrubb  
> Created_at: 2015-11-08 19:41:33 UTC  
> Url: https://github.com/boostorg/graph/pull/34#issuecomment-154860608  

Any hope for merging this soon?

---

## Comment 4

> Username: jaredgrubb  
> Created_at: 2016-04-19 07:06:17 UTC  
> Url: https://github.com/boostorg/graph/pull/34#issuecomment-211763408  

Ping?

---
