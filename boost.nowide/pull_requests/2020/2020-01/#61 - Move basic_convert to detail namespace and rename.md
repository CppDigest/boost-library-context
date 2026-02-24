# #61 Move basic_convert to detail namespace and rename [Merged]

> Username: Flamefire  
> Created at: 2020-01-09 14:18:00 UTC  
> Updated at: 2020-01-24 09:20:12 UTC  
> Merged at: 2020-01-24 09:20:08 UTC  
> Closed at: 2020-01-24 09:20:08 UTC  
> Url: https://github.com/boostorg/nowide/pull/61  

The new names convey what the result will be.  
Also remove helper functions to reduce number of variations.

---

## Comment 1

> Username: Flamefire  
> Created_at: 2020-01-09 14:22:28 UTC  
> Url: https://github.com/boostorg/nowide/pull/61#issuecomment-572582683  

@pdimov @artyom-beilis If you want to have a say feel free. This is based on the observation of the quite full header and the request of https://github.com/boostorg/nowide/issues/33. I find the new names very helpful in understanding the header and having only a single convert_* function keeps the (internal) interface simple

---
