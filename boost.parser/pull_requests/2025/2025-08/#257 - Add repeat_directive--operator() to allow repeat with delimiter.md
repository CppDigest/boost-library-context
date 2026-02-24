# #257 Add repeat_directive::operator() to allow repeat with delimiter [Closed]

> Username: andreasbuhr  
> Created at: 2025-08-04 11:32:44 UTC  
> Updated at: 2026-01-26 14:15:34 UTC  
> Closed at: 2026-01-26 14:15:34 UTC  
> Url: https://github.com/boostorg/parser/pull/257  

Implement request in https://github.com/boostorg/parser/issues/231  
  
I think this is what @psiha is asking for.  
  
I implemented it in operator() to pass two parsers to repeat_directive. This is not so nice. But with operator[] we get only one argument. Do you have any other idea?

---

## Comment 1

> Username: tzlaine  
> Created_at: 2025-10-13 02:23:45 UTC  
> Url: https://github.com/boostorg/parser/pull/257#issuecomment-3395661740  

I'm not sure about this one.  It looks completely different from other directives.  The [] is a pretty important clue how to read a parser.  This might be something to push until C++23 is more widespread.  That allows multiple op[] params.

---
