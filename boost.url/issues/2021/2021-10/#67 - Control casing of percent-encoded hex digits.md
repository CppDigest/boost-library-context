# #67 - Control casing of percent-encoded hex digits [Closed]

> Username: vinniefalco  
> Created at: 2021-10-14 14:12:21 UTC  
> Updated at: 2022-03-16 17:23:08 UTC  
> Closed at: 2022-03-16 17:23:08 UTC  
> Url: https://github.com/boostorg/url/issues/67  

When `url` applies percent-encoding the user should be able to control the capitalization of hex digits A-F, probably through `pct_encode_opts`.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-03-12 03:13:42 UTC  
> Url: https://github.com/boostorg/url/issues/67#issuecomment-1065800488  

Normalization mandates A-F to be uppercase, so it's a probably good idea to just go with that.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-03-16 17:23:08 UTC  
> Url: https://github.com/boostorg/url/issues/67#issuecomment-1069379112  

That's fair
