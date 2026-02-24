# #497 - tilde is special [Closed]

> Username: vinniefalco  
> Created at: 2022-09-03 22:19:24 UTC  
> Updated at: 2022-10-26 22:41:28 UTC  
> Closed at: 2022-10-26 22:41:28 UTC  
> Url: https://github.com/boostorg/url/issues/497  

> The octet corresponding to the tilde ("~") is permitted in query strings by RFC3986 but required to be percent-encoded in HTML forms to "%7E".  
  
This should be added to `encode_opts`

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-09-04 02:07:35 UTC  
> Url: https://github.com/boostorg/url/issues/497#issuecomment-1236232883  

How is this different from encoding with the allowed charset `cs - '~'`?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-09-04 02:18:29 UTC  
> Url: https://github.com/boostorg/url/issues/497#issuecomment-1236234642  

maybe it isn't :) less work for us.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-09-04 02:18:40 UTC  
> Url: https://github.com/boostorg/url/issues/497#issuecomment-1236234680  

I think we have a problem with parsing urlencodings which are part of forms anyway.
