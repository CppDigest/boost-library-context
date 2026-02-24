# #99 - support subtracting histograms if the storage supports negative counter values [Closed]

> Username: HDembinski  
> Created at: 2018-09-21 10:00:56 UTC  
> Updated at: 2019-02-03 18:02:34 UTC  
> Closed at: 2019-02-03 18:02:34 UTC  
> Url: https://github.com/boostorg/histogram/issues/99  



---

## Comment 1

> Username: ahhz  
> Created at: 2018-10-03 12:31:19 UTC  
> Url: https://github.com/boostorg/histogram/issues/99#issuecomment-426619990  

My use case for subtracting histograms would be to have a moving window. I would only subtract histograms that were previously added. So therefore it would not rely on negative counter values. I don't think you need to support negative counter values.   
  
Say, I might create daily histograms from Monday to Sunday, as well as a running total of Last Week. Each day at the end of the day, let's say Tuesday  I would subtract the histogram of last week's Tuesday and add the histogram of this week's Tuesday.

---

## Comment 2

> Username: veprbl  
> Created at: 2018-10-03 15:18:27 UTC  
> Updated at: 2018-10-03 15:39:49 UTC  
> Url: https://github.com/boostorg/histogram/issues/99#issuecomment-426676626  

@ahhz Let me illustrate how I see the issue by referring to your example. A histogram, the way it is defined in this package, tracks two sums: sum of weights for all the fill calls (think number of events) and sum of weights squared (variance, assuming Poisson statistics). One somewhat sensible way to subtract two histograms would be to just flip the sign of the weights of the subtrahend histogram and then do the addition. Notice how this does not affect the sign of weights squared, so your variance will start accumulating as you add positive weights squared. In the end of the day your algorithm produces a histogram with a completely valid number of events, but, if you request variance, you will get a "wrong" overblown value.  
  
In the end of the day we have three desired properties:  
  
 * Histograms track statistical information (e.g. variance)  
 * No API method takes unexpected assumptions or has "surprising" effects on information in the histograms (like overblown variance)  
 * Histograms provide a subtraction method (that allows you to easily implement that optimization)  
  
It seems to me that we can only have two at a time. Then we just need to weight our priorities and make a choice.

---

## Comment 3

> Username: ahhz  
> Created at: 2018-10-03 15:46:14 UTC  
> Url: https://github.com/boostorg/histogram/issues/99#issuecomment-426687497  

I don't get your illustration, except that to me it indicates that using negative weights is not the correct   
way for the case that I describe because it screws up variance.   
  
Providing a subtraction method would be quite straightforward: just subtract the counts and subtract the sum of squared weights. I don't see how it interferes with the other two properties.

---

## Comment 4

> Username: veprbl  
> Created at: 2018-10-03 15:53:18 UTC  
> Url: https://github.com/boostorg/histogram/issues/99#issuecomment-426690324  

@ahhz Subtraction of squared weights yields correct variance only if all events that went into the subtrahend histogram are a subset of events that went into the minuend histogram. This is a very strong assumption.

---

## Comment 5

> Username: jpivarski  
> Created at: 2018-10-03 15:56:49 UTC  
> Url: https://github.com/boostorg/histogram/issues/99#issuecomment-426691623  

And yet, negative weights are absolutely essential for a major user community. The Monte Carlo generators that produce occasional negative weights are usually net-positive, but a histogram bin could be net-negative due to statistical fluctuation.  
  
For your use-case @ahhz, you want subtraction to subtract the sum of weights in each bin (i.e. "counts") and also subtract the sum of weights squared in each bin.  
  
I think a user would only be surprised if they use subtraction and thought it would add the sum of weights in each bin— in other words, that the histogram object was assuming that the two histograms are independent. _That's_ too strong of an assumption, and it's a common trap for ROOT users who divide histograms (ROOT assumes independence, but the user is often dividing a superset from a subset).  
  
Given this sliding window use-case, I think it would make sense to have a subtraction method that subtracts sum of weights and also subtracts sum of weights squared. This doesn't need to be the C++ `operator-`, it can be buried in an "algorithms" library with a name that clearly states its function.

---

## Comment 6

> Username: veprbl  
> Created at: 2018-10-03 16:12:33 UTC  
> Url: https://github.com/boostorg/histogram/issues/99#issuecomment-426697667  

Speaking of dividing, YODA has ```efficiency``` method which assumes the "subset" condition. It is nice to use even though it's not 100% "safe". Changing the name of the method probably makes it a middle ground between giving up items 2 and 3 on my list.

---

## Comment 7

> Username: jpivarski  
> Created at: 2018-10-03 16:22:52 UTC  
> Updated at: 2018-10-03 16:23:31 UTC  
> Url: https://github.com/boostorg/histogram/issues/99#issuecomment-426701405  

Names like the following?  
   * `independent_subtract(h1, h2)` (counts subtract, variances add)  
   * `subset_subtract(h1, h2)` (counts subtract, variances subtract)  
   * `independent_divide(h1, h2)` (counts divide, variances add in percent-error squared)  
   * `subset_divide(h1, h2, method)` (counts divide, what the variances do depends on method)  
  
Statisticians don't have a definitive method for `subset_divide`: options are given in [ROOT's TEfficiency documentation](https://root.cern.ch/doc/v608/classTEfficiency.html) (scroll to "IV. Statistic options"). A respectable implementation wouldn't have to include all of them; "normal approximation", "Wilson", and "Clopper-Pearson" would be a good start.  
  
Or the statistical assumptions could be in a namespace: `independent::` vs `subset::`.

---

## Comment 8

> Username: ahhz  
> Created at: 2018-10-03 17:02:34 UTC  
> Url: https://github.com/boostorg/histogram/issues/99#issuecomment-426716213  

Are these established names? If so, don't mess with it. Otherwise, I wonder if  more descriptive names are possible : subtract_dependent could be called retract, because you are "uncounting" elements that are in the histogram. subtract_independent could be "difference" or "distance" because you are not really changing a histogram but rather getting the difference between two histograms. Under that rationale it should not return a histogram type but a histogram_distance type.  Where histogram counts are unsigned, histogram_difference counts would be signed. Similar to the distance operation on iterators. Likewise, divide_dependent could be "share" because it expresses for each bin how much the small histogram is part of the large histogram. And finally divide_independent could be "ratio" because it expresses how prevalent observations in each bin are compared in one histogram or the other.

---

## Comment 9

> Username: jpivarski  
> Created at: 2018-10-03 17:08:21 UTC  
> Url: https://github.com/boostorg/histogram/issues/99#issuecomment-426718191  

My suggestions weren't established names, but I don't think there are any yet (normally, it's done through the operator).  
  
Your names, @ahhz, are good ones. I especially like `retract` and `ratio`.  
  
A histogram difference type sounds formally right, like time difference types, but I can't think of any methods that would be missing for histogram differences that would be in histograms. Random deviate, perhaps? (Though random deviates would be impossible from any histogram with negative bins.)

---

## Comment 10

> Username: ahhz  
> Created at: 2018-10-03 17:16:26 UTC  
> Url: https://github.com/boostorg/histogram/issues/99#issuecomment-426721109  

I would be uncomfortable with negative bin counts, hence the difference_type. But beyond that I have no good reason for it.

---

## Comment 11

> Username: jpivarski  
> Created at: 2018-10-03 17:19:08 UTC  
> Url: https://github.com/boostorg/histogram/issues/99#issuecomment-426722060  

You can get negative bin counts without a difference via negative weights, so they can't be fully segregated to a different type. Maybe the lack of added value is not worth the added complexity (unlike time difference vs absolute time, which has significant added value).

---

## Comment 12

> Username: ahhz  
> Created at: 2018-10-03 17:42:13 UTC  
> Url: https://github.com/boostorg/histogram/issues/99#issuecomment-426729898  

Yes, then there seems little point to differentiate. "retract" and "share" would not make sense on a histogram_difference, but I don't think the library must police such misuse.

---

## Comment 13

> Username: HDembinski  
> Created at: 2019-02-03 18:02:34 UTC  
> Url: https://github.com/boostorg/histogram/issues/99#issuecomment-460073762  

subtracting histograms is now possible in the develop branch
