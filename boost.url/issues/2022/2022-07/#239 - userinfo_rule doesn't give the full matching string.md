# #239 - userinfo_rule doesn't give the full matching string [Closed]

> Username: vinniefalco  
> Created at: 2022-07-22 17:06:15 UTC  
> Updated at: 2022-08-03 20:38:14 UTC  
> Closed at: 2022-08-03 20:38:14 UTC  
> Url: https://github.com/boostorg/url/issues/239  

For callers who want to treat userinfo as a unit, `userinfo_rule` should additionally offer the entire matching percent-encoded string.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-03 20:38:14 UTC  
> Url: https://github.com/boostorg/url/issues/239#issuecomment-1204458220  

Obsolete as they can just parse an authority without a host and access the `authority_view`. Also, `userinfo_rule` is private :)
