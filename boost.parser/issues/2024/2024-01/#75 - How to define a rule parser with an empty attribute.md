# #75 - How to define a rule parser with an empty attribute? [Closed]

> Username: akrzemi1  
> Created at: 2024-01-25 20:51:36 UTC  
> Updated at: 2024-01-28 22:45:40 UTC  
> Closed at: 2024-01-28 22:45:40 UTC  
> Url: https://github.com/boostorg/parser/issues/75  

The docs do not state it clearly, how I can define a rule parser with an empty attribute (e.g., when I need a parser for skipping comments). Neither in the tutorial, nor in the reference section?  
  
I am guessing that I need to skip the second template parameter. Is that right?

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-01-26 01:29:05 UTC  
> Url: https://github.com/boostorg/parser/issues/75#issuecomment-1911264501  

Yes, that's right.  I'll add this to the docs in a way that's more clear.  I swear it *used* to say that....
