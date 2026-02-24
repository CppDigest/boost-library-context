# #689 Fixed int -> float truncation warning in pixel.hpp [Closed]

> Username: jsenn  
> Created at: 2022-06-17 18:29:00 UTC  
> Updated at: 2022-06-17 19:23:04 UTC  
> Closed at: 2022-06-17 19:23:04 UTC  
> Url: https://github.com/boostorg/gil/pull/689  

### Description  
  
The underlying channel type for grayscale pixels is a 32-bit float. If `Channel` cannot be losslessly converted to a float--for example, if it is `int`--this would generate a compiler warning. The fix here is to perform an explicit cast. Fixes #688.  
  
### References  
  
See related issue: #688.

---

## Comment 1

> Username: jsenn  
> Created_at: 2022-06-17 19:23:04 UTC  
> Url: https://github.com/boostorg/gil/pull/689#issuecomment-1159167056  

The proposed change fixes the warning for the sample code given, but fails template instantiation for pixels that have a raw channel type (i.e., that don't use `scoped_channel_value`).  
  
Also, on further inspection I'm not sure that a simple cast is even warranted here--the warning in #688 results from a piece of code in numeric.hpp that's multiplying 2 shorts (producing an int) and then assigning it to a 32-bit float grayscale pixel (which is scoped to [0, 1]). I'm not sure what the correct behaviour even is there, so I'll close this and let someone more knowledgeable figure this out.

---
