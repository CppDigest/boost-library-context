# #968 - ssl::stream is movable [Closed]

> Username: vinniefalco  
> Created at: 2018-01-05 14:43:17 UTC  
> Updated at: 2018-01-05 18:50:11 UTC  
> Closed at: 2018-01-05 18:50:11 UTC  
> Url: https://github.com/boostorg/beast/issues/968  

We can remove the ssl stream wrapper since it looks like ssl::stream is movable.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-05 18:50:11 UTC  
> Url: https://github.com/boostorg/beast/issues/968#issuecomment-355634346  

I was mistaken, `ssl::stream` is not movable.
