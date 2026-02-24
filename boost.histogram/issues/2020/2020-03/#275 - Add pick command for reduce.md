# #275 - Add pick command for reduce [Open]

> Username: HDembinski  
> Created at: 2020-03-19 19:22:58 UTC  
> Updated at: 2021-05-18 09:41:59 UTC  
> Url: https://github.com/boostorg/histogram/issues/275  

Copied from #267:  
  
scikit-hep/boost-histogram#296  
suggests that one should be able to pick individual bin indices from a category axis. The suggested command is `pick(index1, index2, ...)`, as suggested by @henryiii  
  
The idea is good, but the implementation cost is not negligible. `pick` would only work for unordered axes like category. A new kind of reduce constructor must be defined and a corresponding reduce implementation must be written.

---

## Comment 1

> Username: henryiii  
> Created at: 2020-07-03 16:37:09 UTC  
> Url: https://github.com/boostorg/histogram/issues/275#issuecomment-653617973  

This would also be (very) useful for boolean, and integer, too, though it can be imitated fairly easily currently. I would expect it to work on all axes, for consistency, and sometimes that could actually be useful as well. For example, if you use a Regular axes to collect a range of dates, and you want to select one bin's worth of dates and look at the remaining remaining axes.

---

## Comment 2

> Username: HDembinski  
> Created at: 2020-07-05 06:41:27 UTC  
> Url: https://github.com/boostorg/histogram/issues/275#issuecomment-653849201  

It is definitely useful. This issue was stuck for a while, because it required a rewrite of the algorithm that indentifies and copies the corresponding bins from the old histogram to the new histogram. I had to write this new algorithm to make histograms with category axes addable, so this feature is less far away from reality as before.

---

## Comment 3

> Username: henryiii  
> Created at: 2021-05-17 16:08:12 UTC  
> Url: https://github.com/boostorg/histogram/issues/275#issuecomment-842449212  

There are actually two distinct needs here. Using straw man terms for illustration, not intended to be the recommended names, just pointing out the two distinct uses:  
  
1. `pick_one(axis_number, index)` - this would remove an axis by selecting one item, dropping the rest. In UHI terms, this would be used when you write something like  `h[:, 3, :]` -> `bhalg::reduce(h, bhalg::pick_one(1, 3))`. You could reduce over multiple of these to "pick" multiple axes. This would work on all axes types, and would remove an axis. Due to the changing return type, maybe these would need to be applied to a new `bhalg::pick_reduce` function, not the normal `reduce`? In return type, it's actually similar to `project`.  
2. `pick_several(axis_number, indexes)` - this would pick several items from an axes. UHI terms: `h[:, ["one", "two"], :]` -> `bhalg::reduce(h, bhalg::pick_several(1, {"one", "two"}))`. The return histogram axes would be the same as the input axes, just with smaller category axes (and eventually, integer axes could become category, etc).

---

## Comment 4

> Username: HDembinski  
> Created at: 2021-05-18 09:41:59 UTC  
> Url: https://github.com/boostorg/histogram/issues/275#issuecomment-843021119  

It should be just one `pick` command, but the equivalent of `pick_one` can be work with any axis, while `pick_several` can currently only work for the category axis.  
  
The limitation of the current implementation of reduce is that the axis types cannot change. A future rewrite of reduce should support converting axis types, to support `pick` on an integer axis, which would then produce a category axis.  
  
It has low priority, but we could also add a `discontinuous` axis (straw man name), which would allow for gaps between bins. With this one could also pick from the regular or variable axis.
