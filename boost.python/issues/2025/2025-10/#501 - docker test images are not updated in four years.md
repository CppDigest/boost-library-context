# #501 - docker test images are not updated in four years [Open]

> Username: e-kwsm  
> Created at: 2025-10-28 21:50:03 UTC  
> Updated at: 2025-10-29 00:54:08 UTC  
> Url: https://github.com/boostorg/python/issues/501  

https://github.com/boostorg/python/blob/2b6f667e987c81e91e7cf805e15ec19863f83ed3/.github/workflows/test-ubuntu.yml#L20-L23  
  
The docker images are based on Ubuntu 20.04, whose standard support is ended, and Boost is 1.76.0 as shown by the tags.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2025-10-28 22:47:05 UTC  
> Url: https://github.com/boostorg/python/issues/501#issuecomment-3458860384  

@teeks99, can you confirm what the most recent versions of docker images are ?

---

## Comment 2

> Username: teeks99  
> Created at: 2025-10-28 23:46:02 UTC  
> Url: https://github.com/boostorg/python/issues/501#issuecomment-3459023438  

Yikes! Those are old!!!  
  
I can build new ones pretty quickly, is there a compiler you'd like? clang-21 and gcc-15?

---

## Comment 3

> Username: e-kwsm  
> Created at: 2025-10-29 00:54:08 UTC  
> Url: https://github.com/boostorg/python/issues/501#issuecomment-3459213835  

Thank you for your quick action.
