# #742 X3: position_tagged without inheritance [Closed]

> Username: denzor200  
> Created at: 2022-11-23 17:39:39 UTC  
> Updated at: 2025-05-09 09:35:31 UTC  
> Closed at: 2025-05-09 09:35:30 UTC  
> Url: https://github.com/boostorg/spirit/pull/742  

This PR resolves https://github.com/boostorg/spirit/issues/698.  
It needs to make Spirit more friendly with PFR limitations.  
  
I did this feature only and only for sequence parser(I can't find any reason to make it for another parsers).  
Also, this feature won't support size one sequences.

---

## Comment 1

> Username: saki7  
> Created_at: 2025-05-09 09:35:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/742#issuecomment-2865859762  

I'm afraid that this approach is not robust and thus potentially introducing another layer of complexity on X3's implementation details.  
  
I am also one of the users who are disturbed by the intrusive nature of `position_tagged`. I've even created my own utility for tracking positions non-intrusively. That kind of workaround can be done by just defining your own error handler, since all rules will eventually be passed onto the handler, which essentially provides you with an opportunity to *track* positions for any rules.  
  
Your approach seems to require injecting `position_tagged` on the attributes. Even if this approach is seemingly working on your specific code, I'd rather think the solution is way too complicated than the nature of the issue. There are more fundamental issues regarding the composition and subsumption of variant substitutes, which can be affected by exposing `position_tagged` as an alternative attribute.

---
