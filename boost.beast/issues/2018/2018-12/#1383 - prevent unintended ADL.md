# #1383 - prevent unintended ADL [Closed]

> Username: vinniefalco  
> Created at: 2018-12-20 19:26:13 UTC  
> Updated at: 2019-02-23 03:39:17 UTC  
> Closed at: 2019-02-23 03:39:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1383  

By putting affected class templates into their own namespace, like this:  
https://github.com/boostorg/core/blob/develop/include/boost/core/noncopyable.hpp#L21  
  
Affected classes include  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/http/basic_parser.hpp#L171  
`http::message` (for `Fields`)  
etc

---

## Comment 1

> Username: stale[bot]  
> Created at: 2019-01-20 10:47:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1383#issuecomment-455855483  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-02-02 20:59:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1383#issuecomment-459998695  

I actually don't understand this, how does the extra namespace help?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-02-23 03:39:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1383#issuecomment-466612698  

This is an ongoing concern
