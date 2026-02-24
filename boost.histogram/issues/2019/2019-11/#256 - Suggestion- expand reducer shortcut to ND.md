# #256 - Suggestion: expand reducer shortcut to ND [Closed]

> Username: henryiii  
> Created at: 2019-11-27 14:21:14 UTC  
> Updated at: 2020-01-06 00:44:20 UTC  
> Closed at: 2020-01-06 00:44:20 UTC  
> Url: https://github.com/boostorg/histogram/issues/256  

Currently, there are two forms for each reducer, one that takes an axis number and works in ND histograms, and one that does not take an axis and only works in 1D. This could be generalized fairly naturally, I believe, to ND as well:  
  
* The behavior of the axis version stays the same  
* The non-axis version of reducers are allowed if the number of reducers matches N, the dimensionally of the histogram:  
  
```cpp  
h.reduce(rebin(2), rebin(3))  
```  
  
would be supported in if `h` is a 2D histogram. If it was not a 2D, this would throw an exception, as it does now. Then `h1.reduce(rebin(2))` is no longer a special case, but just the 1D form of the above rule.[^1]  
  
* Axis and non-axis forms could not be mixed in one call to reduce  
* An identity (do nothing to this axis) tag could be added, to keep from having to write something like `rebin(1)`  
  
This would add one of the benefits of the Python's UHI system (easy per-axis application) without adding new syntax, just generalizing a current one. This has a readability benefit, as you are guaranteed to see what happens on each axis.  
  
Thoughts? Is this possible/reasonable/a good idea?  
  
---  
  
[^1]: Currently, for 1D histograms, listing multiple reducers without an axis is supported, and they all apply to axis 0. This could become a special case for 1D to preserve backward compatibility.

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-11-28 17:16:16 UTC  
> Url: https://github.com/boostorg/histogram/issues/256#issuecomment-559565323  

You are right, this should be added. Perhaps not too hard to implement on top of what we have...

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-11-28 17:18:41 UTC  
> Url: https://github.com/boostorg/histogram/issues/256#issuecomment-559565900  

These are good ideas, but I wouldn't take it as far as adding spacers. When you need a spacer, you might be better off just using the other type of interface. I like that the rule for 1D is no longer special, that's very nice thinking.

---

## Comment 3

> Username: HDembinski  
> Created at: 2020-01-06 00:44:20 UTC  
> Url: https://github.com/boostorg/histogram/issues/256#issuecomment-570966366  

Closed in develop
