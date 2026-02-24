# #221 - accelerate bin indexing with offset [Closed]

> Username: HDembinski  
> Created at: 2019-09-28 11:09:13 UTC  
> Updated at: 2019-10-11 21:39:45 UTC  
> Closed at: 2019-10-11 21:39:45 UTC  
> Url: https://github.com/boostorg/histogram/issues/221  

The current code handles axis with underflow and without by shifting the index obtained from by axis by one if the axis has underflow.  
  
```  
linear_index = (i0  + 1) * stride0 + (i1 + 1) * stride1 + ...  
```  
For N axes, this does 2N - 1 additions and N multiplications.  
  
An alternative approach is to compute an offset once when the histogram is constructed, and then add to this offset.  
```  
offset = stride0 + stride1 + ...  
linear_index = offset + i0 * stride0 + i1 * stride1 + ...  
```  
For N axes, this does N additions and N multiplications.  
  
In theory, this should be faster for N > 1 and equally fast for N == 1. It may also simplify the code.

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-10-11 21:39:45 UTC  
> Url: https://github.com/boostorg/histogram/issues/221#issuecomment-541233164  

Implemented in develop
