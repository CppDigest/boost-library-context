# #231 - repeat[] w/ separator [Closed]

> Username: psiha  
> Created at: 2025-05-05 12:15:56 UTC  
> Updated at: 2026-02-22 03:41:27 UTC  
> Closed at: 2026-02-22 03:41:27 UTC  
> Url: https://github.com/boostorg/parser/issues/231  

Could you please add this?  
Otherwise we're forced into this (p for parser, s for separator)  
`p > 's' > p > 's' > ( p % 's' )`  
for a parser separated by s matched at least 3 times...

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-07-12 20:19:22 UTC  
> Url: https://github.com/boostorg/parser/issues/231#issuecomment-3066018963  

I don't think I get what you're asking for.  Could you show what you'd write with the change you have in mind, and describe what the semantics would be?

---

## Comment 2

> Username: psiha  
> Created at: 2026-02-10 09:32:42 UTC  
> Url: https://github.com/boostorg/parser/issues/231#issuecomment-3876443317  

well anything basically that would offer another argument to repeat with which one could specify the separator  
it could literally be an additional 'plain' arugment to repeat(...), which expands the combinatorial vararg hell  
or a specially typed additional arugment: repeat( ..., sep( ":" ) )  
or a wrapper around repeat: sep( repeat(...), ":" )  
or a different function repeat_with_sep()...

---

## Comment 3

> Username: tzlaine  
> Created at: 2026-02-14 20:03:49 UTC  
> Url: https://github.com/boostorg/parser/issues/231#issuecomment-3902434097  

Ah!  Thanks, that makes sense now.  I got caught up on the example, and where you'd want to put the separator, but your follow up answer clarifies.
