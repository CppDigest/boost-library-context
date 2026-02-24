# #608 Revert "Add handling with `optional<Source>` for `boost::spirit::x3::traits::…" [Merged]

> Username: Kojoley  
> Created at: 2020-06-07 16:40:58 UTC  
> Updated at: 2020-06-07 21:47:43 UTC  
> Merged at: 2020-06-07 21:47:41 UTC  
> Closed at: 2020-06-07 21:47:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/608  

Reverts boostorg/spirit#534  
  
When a parser attribute type is `optional` it means that it may not produce a value, and in that case target attribute will left uninitialized. If we ever want to support this, it should be done somehow explicitly.  
  
Also, the test does not compile, it is not run by CI because it was not add to `Jamfile`.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2020-06-07 21:47:32 UTC  
> Url: https://github.com/boostorg/spirit/pull/608#issuecomment-640284791  

I was thinking of putting a static assert, but there is no single good place, I probably get to it later on.

---
