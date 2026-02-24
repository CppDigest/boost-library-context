# #806 Update sexpr_parser.hpp [Closed]

> Username: rcoca  
> Created at: 2025-06-08 14:45:31 UTC  
> Updated at: 2025-06-29 05:19:56 UTC  
> Closed at: 2025-06-27 23:35:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/806  

updated rule for list - to have it captured in utree::list_type - and thus preserve the actual structure of the parsing tree

---

## Comment 1

> Username: saki7  
> Created_at: 2025-06-15 18:03:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/806#issuecomment-2974426512  

Could you elaborate on why this fix is needed in the first place? I think the change is straightforward, but the PR is seriously lacking the context, I guess.

---

## Comment 2

> Username: saki7  
> Created_at: 2025-06-27 23:35:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/806#issuecomment-3014650525  

I tested the example, and it turns out the code compiles with or without the fix.  
  
`utree::list_type` has a conversion constructor that allows transparent construction from `utree`, so there's no compelling reason to expose the *internal* list type in the example. While your version looks more strict in terms of parser attributes, I'm not convinced that's a sufficient reason to change a 15-year-old example.  
  
Since I haven't received any further information since my last comment, I'm closing this for now. If you still believe the change is necessary, please explain your reasoning.

---

## Comment 3

> Username: rcoca  
> Created_at: 2025-06-29 05:19:12 UTC  
> Updated_at: 2025-06-29 05:19:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/806#issuecomment-3016319255  

Sorry, I saw your message late.  
I trust you judgement here, the parser runs just fine and it does syntax check.  
It just builds a list - instead of an AST. But (AST) that's probably not example's aim.

---
