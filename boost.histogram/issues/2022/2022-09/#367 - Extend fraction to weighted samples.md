# #367 - Extend fraction to weighted samples [Open]

> Username: HDembinski  
> Created at: 2022-09-28 12:11:34 UTC  
> Updated at: 2022-11-15 11:24:07 UTC  
> Url: https://github.com/boostorg/histogram/issues/367  

We have a fraction accumulator now, but it does not work correctly for weighted samples. However, we definitely need this in high-energy physics because we often estimate the efficiency of some detector from weighted simulation samples.  
  
There are several ways how this could be implemented  
1) New class `weighted_fraction<T>`  
2) Specialization `fraction<weighted_sum<T>>`  
3) Template specialization `fraction<T, bool weight_support = False>`  
  
Number 3) is my least favored. I like 2), if that's feasible.  
  
Ping @henryiii @gohil-jay @jpivarski

---

## Comment 1

> Username: HDembinski  
> Created at: 2022-09-28 13:14:17 UTC  
> Url: https://github.com/boostorg/histogram/issues/367#issuecomment-1260896487  

For those interest, the math for weighted fractions is discussed here https://arxiv.org/abs/2110.00294
