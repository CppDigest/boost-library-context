# #53 - Memory layout consistantency with graphcis APIs [Closed]

> Username: felixaszx  
> Created at: 2024-08-28 17:56:25 UTC  
> Updated at: 2024-08-28 18:47:24 UTC  
> Closed at: 2024-08-28 18:43:50 UTC  
> Url: https://github.com/boostorg/qvm/issues/53  

Looks like `qvm` has a memory layout differed from common graphics APIs like VK, DX and GL.  
for matrix that translate to `[1, 1, 1]` will have a memory layout of `{1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 1, 1, 1, 1}` in all mentioned apis above which is colum-major despect the fact that DX and GL(VK) have different interpretation base on that.  
However, seems like `qvm` will have the layout of `{1, 0, 0, 1, 0, 1, 0, 1, 0, 0, 1, 1, 0, 0, 0, 1}` which is row-major.  
Is it possible to ensure a memory layout consistency between `qvm` and graphics APIs?

---

## Comment 1

> Username: zajo  
> Created at: 2024-08-28 18:43:50 UTC  
> Updated at: 2024-08-28 18:47:24 UTC  
> Url: https://github.com/boostorg/qvm/issues/53#issuecomment-2316027857  

QVM is independent of any particular memory layout. Logically, access is always in terms of Row, Column, and in a 4x4 matrix the translation occupies elements <3,0>, <3,1>, <3,2> (zero-based). Physically, the data can be stored in row-major, col-major, or only part of the elements may be stored, etc. This is up to the user.  
  
If you're using a graphics API that defines a matrix type, simply specialize the qvm::mat_traits template for that type, and voila -- you can use any compatible QVM operation on that type, and it will use the physical layout of that type.
