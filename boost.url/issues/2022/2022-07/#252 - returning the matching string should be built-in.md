# #252 - returning the matching string should be built-in [Open]

> Username: vinniefalco  
> Created at: 2022-07-27 14:31:40 UTC  
> Updated at: 2022-11-03 23:55:45 UTC  
> Url: https://github.com/boostorg/url/issues/252  

Currently, rules have to manually offer a view to the matching string in addition to the semantically relevant parts in the value type. `grammar::parse` can trivially obtain this information, but we need an interface for the user to access it.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-10-19 19:00:16 UTC  
> Updated at: 2022-10-19 19:00:35 UTC  
> Url: https://github.com/boostorg/url/issues/252#issuecomment-1284446205  

Is there any case where this would be very helpful? I think `grammar` has changed a lot since then.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-11-03 23:55:45 UTC  
> Url: https://github.com/boostorg/url/issues/252#issuecomment-1302803241  

There is, but this can be explored later
