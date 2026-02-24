# #349 - Review refs [Closed]

> Username: alandefreitas  
> Created at: 2022-08-06 00:41:56 UTC  
> Updated at: 2022-09-11 23:59:20 UTC  
> Closed at: 2022-09-11 23:59:20 UTC  
> Url: https://github.com/boostorg/url/issues/349  

If we `#define BOOST_URL_TUPLE_EBO 0` then it works with or without the refs.  
If we `#define BOOST_URL_TUPLE_EBO 1` then it fails with or without the refs.  
  
So it's probably a problem with EBO. Then something in EBO must be influencing the addresses and making find_if_not ignore ".". This is my best guess so far.  
  
Regarding what's actionable:  
- `ref` is not causing any problem right now, but we could make it private if we have concerns about it.  
- I have no idea how to improve the problem with tuple. The best I could do is research and ask around.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-11 23:59:20 UTC  
> Url: https://github.com/boostorg/url/issues/349#issuecomment-1243072805  

Seems fine.
