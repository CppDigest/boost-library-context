# #332 - Bin count is limited by `unsigned` [Closed]

> Username: vakokako  
> Created at: 2021-09-24 13:26:42 UTC  
> Updated at: 2021-09-27 09:11:24 UTC  
> Closed at: 2021-09-26 13:57:03 UTC  
> Url: https://github.com/boostorg/histogram/issues/332  

`histogram::axis::regular` constructor takes bins count as `unsigned`. Are there any plans to support bigger types?  
  
There is `histogram::axis::index_type`, maybe adding equivalent for bin count so that this could be a simple patch if somebody would want lets say `size_t` number of bins.

---

## Comment 1

> Username: henryiii  
> Created at: 2021-09-24 14:41:24 UTC  
> Url: https://github.com/boostorg/histogram/issues/332#issuecomment-926682065  

Just curious, do you have an actual use case for more than four billion bins in a single histogram axis?

---

## Comment 2

> Username: HDembinski  
> Created at: 2021-09-26 10:36:25 UTC  
> Url: https://github.com/boostorg/histogram/issues/332#issuecomment-927280912  

What @henryiii said.   
`unsigned` is deemed large enough (by a huge margin) and so there are currently no plans to support a bigger type per axis, but if you have a convincing use-case, I am happy to consider this.

---

## Comment 3

> Username: HDembinski  
> Created at: 2021-09-26 10:37:20 UTC  
> Updated at: 2021-09-26 13:57:28 UTC  
> Url: https://github.com/boostorg/histogram/issues/332#issuecomment-927281086  

Note that a multi-dimensional histogram is allowed to have more than four billion bins. The restriction here is four billion bins **per axis**.

---

## Comment 4

> Username: vakokako  
> Created at: 2021-09-27 09:11:24 UTC  
> Url: https://github.com/boostorg/histogram/issues/332#issuecomment-927677896  

It's true, the number of bins is probably enough. It's mostly for consistency with most other containers, where the `size_t` is used for size.
