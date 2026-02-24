# #247 added missing "::type" in BOOST_FUSION_ADAPT_ADT_C_BASE [Closed]

> Username: tobias-loew  
> Created at: 2022-02-18 12:08:46 UTC  
> Updated at: 2022-02-18 13:38:37 UTC  
> Closed at: 2022-02-18 13:38:37 UTC  
> Url: https://github.com/boostorg/fusion/pull/247  

In access::struct_member::apply the typedef adt_attribute_proxy<...>... lacked a missing "::type".  
ADT-adapted structs didn't work e.g. with fold

---

## Comment 1

> Username: tobias-loew  
> Created_at: 2022-02-18 13:38:37 UTC  
> Url: https://github.com/boostorg/fusion/pull/247#issuecomment-1044540821  

The error was on my business code: my fold-operator expected its second argument as reference (which worked with the former adapted-struct but not with the new adt-adapted-struct).  
Sorry for the trouble.

---
