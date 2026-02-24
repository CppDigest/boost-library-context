# #574 - decode_opts in for query and params [Closed]

> Username: alandefreitas  
> Created at: 2022-09-28 17:19:39 UTC  
> Updated at: 2022-10-21 17:21:38 UTC  
> Closed at: 2022-10-21 17:21:37 UTC  
> Url: https://github.com/boostorg/url/issues/574  

The problem: The RFC expects `%20` in query and params (`%20` is ` ` and `+` is actually `+`) in the general syntax (See the examples https://www.rfc-editor.org/rfc/rfc6068). The API does not provide a way to do that, even if we use the encoded query, because `set_query` would already convert ` ` to `+` (https://github.com/boostorg/url/pull/573#issuecomment-1260259797).  
  
The origin: We are often tending towards treating the general syntax `url_view` as if it were some kind of scheme-based `http_url_view` class. That works 99% of the time and it's also great because it's the most common scheme by far. But it also creates conflicts when the `url_view` is really being used as the general syntax for any other scheme.  
  
The solution: `set_query` needs `encode_opts` in some way. This also implies we would need `params` to carry its own `decode_opts`.  
  
Some open questions:  
  
- This is both a feature (because `encode_opts`/`decode_opts` in the interface) and a bug (because it's causing problems to any scheme that is not http)? Should we wait because it's a feature or should we fix it now because it's a bug?  
- Would `set_query` use the general syntax (`%20`) or the http syntax (`+`) for spaces by default?  
- Something encoded with `space_to_plus` needs to be decoded with `plus_to_space`. Should the url also remember whether query used `space_to_plus` when encoding the query by default? Or should the user always remember the options externally to give it the `decode_opts` with `plus_to_space` whenever decoding? I imagine most people won't understand the logic and fail a lot until they learn because most people might assume whatever was encoded correctly will decode correctly. Maybe my expectation is wrong. Or maybe I'm there's a better API to represent that. The URL could have some `is_http_query` function or something.  
- If the URL carries the `decode_opts`, would `parse_uri` and analogous functions use the general syntax (`%20`) or http syntax (`+`) for query spaces by default initially? How would the user decide the initial value?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-28 17:51:06 UTC  
> Url: https://github.com/boostorg/url/issues/574#issuecomment-1261260330  

> If the URL carries the decode_opts  
  
ugh... stateful URLs.. please, no

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-09-28 17:51:32 UTC  
> Url: https://github.com/boostorg/url/issues/574#issuecomment-1261261362  

Actually, I might be OK if the `url_view` was stateful

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-09-28 18:37:06 UTC  
> Url: https://github.com/boostorg/url/issues/574#issuecomment-1261318530  

:)

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-10-21 17:21:37 UTC  
> Url: https://github.com/boostorg/url/issues/574#issuecomment-1287240808  

There's no more decode-opts
