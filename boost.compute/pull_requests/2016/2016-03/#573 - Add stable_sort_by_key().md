# #573 Add stable_sort_by_key() [Merged]

> Username: jszuppe  
> Created at: 2016-03-16 16:55:27 UTC  
> Updated at: 2016-04-01 03:27:11 UTC  
> Merged at: 2016-03-19 02:36:22 UTC  
> Closed at: 2016-03-19 02:36:22 UTC  
> Url: https://github.com/boostorg/compute/pull/573  

This PR adds `stable_sort_by_key()`. Currently, `stable_sort_by_key()` works on GPUs only for  
keys that are `radix-sortable` and for very small inputs (serial insertion sort algorithm).

---

## Comment 1

> Username: kylelutz  
> Created_at: 2016-03-19 02:36:25 UTC  
> Url: https://github.com/boostorg/compute/pull/573#issuecomment-198620373  

Awesome! Merged!

---

## Comment 2

> Username: pavanky  
> Created_at: 2016-03-31 20:57:41 UTC  
> Updated_at: 2016-03-31 20:59:36 UTC  
> Url: https://github.com/boostorg/compute/pull/573#issuecomment-204127356  

@kylelutz When is the next release expected ? We want to include the function in arrayfire. We are looking for a properly defined way to identify if the functionality is available in a given install of boost compute. If we can tie this down to a version it'd be great.  
  
> Currently, stable_sort_by_key() works on GPUs only for  
> keys that are radix-sortable and for very small inputs (serial insertion sort algorithm).  
  
@haahh Do you mean it has to be both radix sortable and is limited to small sizes or is it either one or the other ?

---

## Comment 3

> Username: jszuppe  
> Created_at: 2016-03-31 21:05:23 UTC  
> Url: https://github.com/boostorg/compute/pull/573#issuecomment-204129559  

Either one or the other. Basically, Boost.Compute radix sort is stable so we used it here, but we do not have comparison sort implementation yet.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2016-04-01 03:27:11 UTC  
> Url: https://github.com/boostorg/compute/pull/573#issuecomment-204229653  

@pavanky Now that Boost.Compute is accepted and merged, the next release will come with Boost 1.61 (currently in beta, schedule to be released in the next couple weeks). After that, I we will follow the normal Boost release cadence of every 3-4 months.

---
