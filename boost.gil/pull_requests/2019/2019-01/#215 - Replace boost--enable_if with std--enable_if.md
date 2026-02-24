# #215 Replace boost::enable_if with std::enable_if [Merged]

> Username: mloskot  
> Created at: 2019-01-17 00:06:47 UTC  
> Updated at: 2019-01-18 08:32:13 UTC  
> Merged at: 2019-01-18 08:31:59 UTC  
> Closed at: 2019-01-18 08:31:59 UTC  
> Url: https://github.com/boostorg/gil/pull/215  

* Replace `boost::disable_if` with `std::enable_if` and negated condition.  
* Update `mpl::and_` expressions to yield value convertible to `bool`, instead of type.  
* Format `std::enable_if` expressions and nearby code they are nested in.  
* Replace complex template-based leading return type of functions with auto and trailing return type for greater readability.  
* Replace some Boost type traits with `<type_traits>` features.  
* Replace 0 with `nullptr` where modernize-use-nullptr missed (updates #180).  
* Notice, `boost::lazy_enable_if` in extension/toolbox has not been replaced, but it seems it could have been - need verification with number of compilers.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Review 1 [Approved]

> Username: stefanseefeld  
> Created_at: 2019-01-17 00:35:24 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/215#pullrequestreview-193417621  

Wonderful, thanks !

---

## Comment 2

> Username: mloskot  
> Created_at: 2019-01-17 09:48:56 UTC  
> Url: https://github.com/boostorg/gil/pull/215#issuecomment-455109120  

Yeah, I'm so happy I've finally moved this one forward!

---
