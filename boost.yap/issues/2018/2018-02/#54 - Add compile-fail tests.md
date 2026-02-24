# #54 - Add compile-fail tests [Closed]

> Username: tzlaine  
> Created at: 2018-02-19 20:52:49 UTC  
> Updated at: 2018-06-11 17:16:20 UTC  
> Closed at: 2018-03-04 04:28:25 UTC  
> Url: https://github.com/boostorg/yap/issues/54  

You need some compile-fail tests:  
- invalid arguments to accessors, such as left, right, and get.  
- possibly switching the arguments of transform.  (I find  
  it mildly disturbing that this currently compiles, as  
  transform is one function for which I may not always remember  
  the correct argument order.)

---

## Comment 1

> Username: tzlaine  
> Created at: 2018-02-22 20:02:05 UTC  
> Url: https://github.com/boostorg/yap/issues/54#issuecomment-367803951  

Don't forget to include transform_strict().
