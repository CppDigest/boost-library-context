# #50 - Consider adding a iota function [Closed]

> Username: ldionne  
> Created at: 2015-04-19 17:40:56 UTC  
> Updated at: 2015-09-15 21:26:45 UTC  
> Closed at: 2015-09-15 21:26:45 UTC  
> Url: https://github.com/boostorg/hana/issues/50  

`iota(from, to)` could create a `Sequence` containing the `IntegralConstant`s, or it could also create a `Range`. Creating a `Range` would be more compile-time efficient, but maybe that's not what we need (we already have `make<Range>`). This also opens the question of whether a custom step should be allowed, how to make this compile-time efficient if we return a `Sequence`, and so on.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-09-15 21:26:45 UTC  
> Url: https://github.com/boostorg/hana/issues/50#issuecomment-140551738  

Not needed; we can already use `make_range` and then convert to the `Sequence` we want, optimally. Adding another function just for that would be bloating the interface for no good reason. Closing.
