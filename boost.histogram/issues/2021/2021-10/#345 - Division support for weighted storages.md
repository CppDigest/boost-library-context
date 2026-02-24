# #345 - Division support for weighted storages [Closed]

> Username: henryiii  
> Created at: 2021-10-06 17:44:53 UTC  
> Updated at: 2022-02-10 09:36:08 UTC  
> Closed at: 2022-02-10 09:36:08 UTC  
> Url: https://github.com/boostorg/histogram/issues/345  

Weighted storages do not have a division operator. @lgray requested this for doing a background estimate.

---

## Comment 1

> Username: lgray  
> Created at: 2021-10-06 18:07:39 UTC  
> Url: https://github.com/boostorg/histogram/issues/345#issuecomment-936817161  

I guess this can be a little difficult when it comes to errors and sumw. I would suggest implementing the simplest rule within boost-histogram and if people need clopper-pearson or something else for division of correlated quantities they can manipulate as needed.

---

## Comment 2

> Username: HDembinski  
> Created at: 2021-11-06 06:09:01 UTC  
> Url: https://github.com/boostorg/histogram/issues/345#issuecomment-962401424  

Agreed. Division generally assumes the data sets are independent. If you compute an efficiency, the data sets are correlated. We cannot know what has to be done in this case so we can only implement the rule for the simplest assumption, independent data sets.  
  
The best way to handle computing efficiencies with Boost Histogram is to use a special efficiency accumulator that still needs to be written, where you pass a boolean to indicate whether the event passed or not.

---

## Comment 3

> Username: lgray  
> Created at: 2021-11-06 14:18:58 UTC  
> Url: https://github.com/boostorg/histogram/issues/345#issuecomment-962458496  

Yes - the latter thing you mention would be a really nice interface!

---

## Comment 4

> Username: HDembinski  
> Created at: 2021-11-15 08:39:50 UTC  
> Url: https://github.com/boostorg/histogram/issues/345#issuecomment-968655669  

> Yes - the latter thing you mention would be a really nice interface!  
  
@lgray If you are interested in drafting such an accumulator, I am happy to review it.  
  
See https://github.com/boostorg/histogram/issues/178

---

## Comment 5

> Username: HDembinski  
> Created at: 2021-12-02 08:33:16 UTC  
> Updated at: 2021-12-02 08:35:29 UTC  
> Url: https://github.com/boostorg/histogram/issues/345#issuecomment-984405145  

Some additional comments:  
- The weighted storage only knows about the value and its variance estimate. Confidence intervals like Clopper-Pearson are something different from a variance estimate, so this cannot be supported by division anyway, and Clopper-Pearson is not applicable to sums of weights.  
- Division by a constant already works correctly.  
- What we want is the special rule for multiplication / division of a `weighted_sum` by another `weighted_sum`, that computes the variance correctly.  
- Division of a `weighted_sum` by an ordinary storage (e.g. `double`) treats the count like a constant and not like a Poisson count with a Poisson variance. This is perhaps not what the user expects, but we have no guarantee that the contents of an ordinary histogram are counts and not something else, and so there is nothing we can do about it. We gave up on that guarantee early on in the design.
