# #584 Add image sharpening algorithm [Open]

> Username: harshitpant1  
> Created at: 2021-03-18 08:25:16 UTC  
> Updated at: 2021-03-18 08:28:16 UTC  
> Url: https://github.com/boostorg/gil/pull/584  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Image sharpening is a practice used frequently in fields like astronomy, printing industries and many other areas of image processing. This PR adds image sharpening functionality (using [unsharp masking](https://en.wikipedia.org/wiki/Unsharp_masking) algorithm) for grayscale and RGB images to GIL.   
  
### References  
  
Principles of Digital Image Processing: Fundamental Techniques by Wilhelm Burger, Mark J. Burge.  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [ ] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: harshitpant1  
> Created_at: 2021-03-18 08:28:15 UTC  
> Url: https://github.com/boostorg/gil/pull/584#issuecomment-801730666  

Note that for this to work, https://github.com/boostorg/gil/pull/577 needs to be looked into first.  
Gentle reminder to @lpranam

---
