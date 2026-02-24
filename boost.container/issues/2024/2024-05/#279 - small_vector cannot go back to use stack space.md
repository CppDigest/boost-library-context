# #279 - small_vector cannot go back to use stack space [Closed]

> Username: igaztanaga  
> Created at: 2024-05-21 21:17:57 UTC  
> Updated at: 2024-06-04 12:06:10 UTC  
> Closed at: 2024-06-04 12:06:10 UTC  
> Url: https://github.com/boostorg/container/issues/279  

As reported in:  
  
https://stackoverflow.com/questions/54429138/boost-small-vector-cannot-go-back-to-use-stack-space  
  
small vector does not go back to the internal storage even after shrink_to_fit. This behavior is explicitly documented for the class but maybe it should be revisited, specially for shrink_to_fit.  
  
The LLVM implementation does not seem to offer this feature, Folly seems to implement construct + swap to achieve it in shrink_to_fit.  
  
In the same line, in current swap implementation, when one vector uses a dynamic buffer and the other one the internal buffer, swap allocates also a dynamic buffer for the vector with the internal buffer. This should be avoided

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-06-04 12:06:10 UTC  
> Url: https://github.com/boostorg/container/issues/279#issuecomment-2147369007  

The commit https://github.com/boostorg/container/commit/aa35950ca0f8fb82e85a4de71eb77901f5f285d6 finishes changes in small_vector so that:  
  
- `shrink_to_fit` migrates data to the internal buffer if size() <= internal_capacity().  
- `swap` acquires the existing dynamic buffer to avoid moving elements, and uses the internal buffer if the dynamic buffer can't be acquired and data fits in the internal buffer.
