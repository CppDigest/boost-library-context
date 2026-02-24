# #278 - Implement over-allocation for histograms with growing axes [Open]

> Username: HDembinski  
> Created at: 2020-04-30 17:54:31 UTC  
> Updated at: 2020-04-30 17:59:45 UTC  
> Url: https://github.com/boostorg/histogram/issues/278  

Histograms with a growing axes should allocate more space than needed, similar to vector's push_back. This requires adding an extra field on an axis to query the spare capacity. A storage is then only resized if the spare capacity is exceeded.

---

## Comment 1

> Username: HDembinski  
> Created at: 2020-04-30 17:55:44 UTC  
> Updated at: 2020-04-30 17:59:45 UTC  
> Url: https://github.com/boostorg/histogram/issues/278#issuecomment-622008515  

We then need a reserve() on the axis and shrink_to_fit() to the histogram.
