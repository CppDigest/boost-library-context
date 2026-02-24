# #161 - Clearly indicate which directives create a new parser when used, which do not, and why it matters. [Closed]

> Username: tzlaine  
> Created at: 2024-03-10 01:17:32 UTC  
> Updated at: 2024-03-15 21:29:35 UTC  
> Closed at: 2024-03-15 21:29:35 UTC  
> Url: https://github.com/boostorg/parser/issues/161  

This should go in the "Directives" page.  It matters because `a >> b >> c` and `d[a >> b] >> c` have different types if `d[]` creates a new parser.  If `d[]` does not (like `merge[]` and `separate[]`), the type of the two original expressions above might be the same (modulo the changes brought by using `merge[]`/`separate[]`).

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-03-10 01:20:51 UTC  
> Url: https://github.com/boostorg/parser/issues/161#issuecomment-1987032690  

Also, add a section to the rationale for why we have two kinds of directives.  The reason is that it is possible (and desirable) to have some directives simply modify their parser, and other times it is not.  `merge[]` just modifies its parser, but `transform(f)` probably cannot -- if it did, we'd have to adjust every single parser to return the transformed attribute instead of the default generated one.
