# #558 - improve nth element speed by using bucketselect or cutting plane instead of quickselect [Open]

> Username: koosha94  
> Created at: 2016-02-26 06:31:35 UTC  
> Updated at: 2017-04-25 15:07:46 UTC  
> Url: https://github.com/boostorg/compute/issues/558  

this work http://www.math.grin.edu/~blanchaj/Research/ABGS_KSelection.pdf , and a few other works such as http://arxiv.org/pdf/1104.2732.pdf, propose gpu accelerated algorithms for nth element selection.  
I believe using bucketselect presented in Alabi et al. would result in a significant speed up while keeping the code simple and elegant.  
##

---

## Comment 1

> Username: koosha94  
> Created at: 2016-02-26 06:37:48 UTC  
> Url: https://github.com/boostorg/compute/issues/558#issuecomment-189131137  

This [https://code.google.com/archive/p/ggks/downloads](url) contains cuda (with simple use of thrust libraries) implementations for all algorithms mentioned in this work which would come in handy.

---

## Comment 2

> Username: kylelutz  
> Created at: 2016-02-28 18:18:13 UTC  
> Url: https://github.com/boostorg/compute/issues/558#issuecomment-189916994  

It would be great to have improvements for these algorithms! Would you be interested in working on that?

---

## Comment 3

> Username: koosha94  
> Created at: 2016-02-28 20:20:37 UTC  
> Url: https://github.com/boostorg/compute/issues/558#issuecomment-189939811  

Unfortunately, I have a few deadline to meet, however I can help testing, and debugging the code.  
I might be able to work on it later in the spring.
