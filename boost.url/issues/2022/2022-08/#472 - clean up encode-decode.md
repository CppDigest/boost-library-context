# #472 - clean up encode/decode [Closed]

> Username: vinniefalco  
> Created at: 2022-08-29 18:07:39 UTC  
> Updated at: 2022-09-11 23:38:08 UTC  
> Closed at: 2022-09-11 23:38:08 UTC  
> Url: https://github.com/boostorg/url/issues/472  

move detail functions to a separate file, use `detail::make_decode_view` instead of `access`, check exception specifications and javadocs, provide an out of line overload for all_chars.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-11 23:38:08 UTC  
> Url: https://github.com/boostorg/url/issues/472#issuecomment-1243068910  

Did it
