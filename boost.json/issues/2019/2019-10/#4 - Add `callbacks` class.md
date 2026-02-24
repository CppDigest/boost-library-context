# #4 - Add `callbacks` class [Closed]

> Username: vinniefalco  
> Created at: 2019-10-13 21:36:09 UTC  
> Updated at: 2019-11-14 15:16:27 UTC  
> Closed at: 2019-11-14 15:16:27 UTC  
> Url: https://github.com/boostorg/json/issues/4  

We can move the implementation of the callbacks in `parser` to a separate class to make the callbacks public, so that it can be re-used to allow third party parsers that support sax to produce `json::value` objects easily.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-11-14 15:16:24 UTC  
> Url: https://github.com/boostorg/json/issues/4#issuecomment-553932819  

Cant' do this now that we have the close tie of the stack to the basic parser's saved state.
