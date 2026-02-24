# #16 - Make `polygon_90_set_view::value()` public [Closed]

> Username: ton  
> Created at: 2017-11-16 10:25:31 UTC  
> Updated at: 2017-11-16 16:47:21 UTC  
> Closed at: 2017-11-16 16:47:21 UTC  
> Url: https://github.com/boostorg/polygon/issues/16  

Is there a reason why `polygon_90_set_view::value()` is not public, in contrast with similar `value()` methods on `polygon_45_set_view` and `boost::polygon_set_view`?  
  
Making it a public method would make it possible to write more generic client code. Suppose for example I have two polygon sets of a certain (unknown) type, A and B. Then, the following code instantiates a polygon set of the correct type, but only in case the set view resulting from the union operation is not a `boost::polygon_90_set_view`.  
  
`auto result{(A | B).value()};`  
  
As far as I can see, there is no fundamental reason why `polygon_90_set_view::value()` can not be made publicly visible.

---

## Comment 1

> Username: asydorchuk  
> Created at: 2017-11-16 15:05:20 UTC  
> Url: https://github.com/boostorg/polygon/issues/16#issuecomment-344950270  

Hi Ton! Please proceed with a pull request and let's make it public.

---

## Comment 2

> Username: ton  
> Created at: 2017-11-16 16:47:07 UTC  
> Url: https://github.com/boostorg/polygon/issues/16#issuecomment-344984289  

Actually, I am no longer sure it is really needed. The following works as well:  
  
```  
using PolygonSet = typename decltype(A | B)::value_type;  
PolygonSet ps{A | B};  
```  
  
So I think this issue can be closed, unless you see another good reason to make `value()` public (e.g. for consistency).
