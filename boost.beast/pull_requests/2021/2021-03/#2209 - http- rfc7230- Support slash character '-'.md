# #2209 http: rfc7230: Support slash character '/' [Closed]

> Username: anoo1  
> Created at: 2021-03-29 20:56:17 UTC  
> Updated at: 2021-03-30 13:35:08 UTC  
> Closed at: 2021-03-30 13:35:08 UTC  
> Url: https://github.com/boostorg/beast/pull/2209  

Some http headers like Accept[1] have a format that includes a slash  
character, example: MIME_type/MIME_subtype;q=factor.  
  
Add support to treat this slash as a regular character.  
  
1: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Accept

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2021-03-29 21:06:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2209#pullrequestreview-623650643  

📁 include/boost/beast/http/detail/rfc7230.ipp

```diff
  84 |-                 "'" | "*" | "+" | "-" | "." |
  85 |-                 "^" | "_" | "`" | "|" | "~" |
  83 |+         tchar = "!" | "#" | "$" | "%" | "&" | "'" | "*" | "+" |
```

> Username: vinniefalco  
> Created_at: 2021-03-29 21:06:41 UTC  
> Url: https://github.com/boostorg/beast/pull/2209#discussion_r603613725  

This function is used by the HTTP parser. Changing it would change what is allowed in HTTP headers.


---

## Comment 2

> Username: vinniefalco  
> Created_at: 2021-03-29 21:07:17 UTC  
> Url: https://github.com/boostorg/beast/pull/2209#issuecomment-809714922  

Is this change intending only to affect the algorithms in rfc7230.hpp?

---

## Comment 3

> Username: anoo1  
> Created_at: 2021-03-29 21:19:03 UTC  
> Url: https://github.com/boostorg/beast/pull/2209#issuecomment-809721499  

> Is this change intending only to affect the algorithms in rfc7230.hpp?  
  
Yeah, I want to parse the http Accept header, and the most straight forward iterator is to use ext_list, but none of the http iterators (ext_list, param_list, etc), handle the '/', instead they stop processing the string when they encounter one.  
  
I noticed there's a "is_text()" function that's not currently being used. Would that be something that could be used during the string processing instead of relying just in "is_token_char()"?

---

## Comment 4

> Username: anoo1  
> Created_at: 2021-03-29 21:31:28 UTC  
> Url: https://github.com/boostorg/beast/pull/2209#issuecomment-809728414  

Maybe the question is, could this be supported:  
```  
boost::beast::http::ext_list{"*/*,text/plain;q=1,text/html;q=0.8"}  
```  
If yes, what would be the recommended changes?  
If not, which parser would you suggest, since the advantage of an ext_list is that it automatically separates the types (*/*,) from the factor (;q=1) so the Accept types can easily be compared to see which format the caller expects, without doing additional parsing like using boost::contains.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2021-03-29 21:54:22 UTC  
> Url: https://github.com/boostorg/beast/pull/2209#issuecomment-809739206  

> could this be supported:  
  
I thought I already implemented that, but if I didn't - yes, it would be nice to have. However, you cannot change `detail::is_token_char` because the HTTP parser uses that, and the RFC does not allow a slash for the places where `is_token_char` is called from the parser. You would need to make your own function, e.g. `is_ext_token_char` and use that instead.

---

## Comment 6

> Username: anoo1  
> Created_at: 2021-03-30 13:35:08 UTC  
> Url: https://github.com/boostorg/beast/pull/2209#issuecomment-810250351  

> You would need to make your own function, e.g. `is_ext_token_char` and use that instead.  
  
Make sense. Thanks, closing the PR.

---
