# #1343 - Document flat_stream, ssl_stream, make them public [Closed]

> Username: vinniefalco  
> Created at: 2018-12-03 15:16:59 UTC  
> Updated at: 2022-06-16 21:24:50 UTC  
> Closed at: 2022-06-16 16:23:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1343  

The documentation should include a note about the performance issues with `asio::ssl::stream`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-23 03:39:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1343#issuecomment-466612728  

`flat_stream` is public now

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-06-16 16:23:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1343#issuecomment-1157873530  

We aren't doing this anymore

---

## Comment 3

> Username: sehe  
> Created at: 2022-06-16 21:24:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1343#issuecomment-1158142856  

I strongly remember seeing that Chris added buffer flattening to the ssl::stream implementation in Asio proper.  
  
That might make beast's flat_stream redundant (?), but at the very least means that this issue was actually resolved.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-06-16 21:24:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1343#issuecomment-1158143353  

Yes it does make it redundant
