# #64 Fix potentially uninitialized local variable y [Merged]

> Username: mloskot  
> Created at: 2018-03-27 11:45:34 UTC  
> Updated at: 2018-03-27 13:00:17 UTC  
> Merged at: 2018-03-27 12:53:22 UTC  
> Closed at: 2018-03-27 12:53:22 UTC  
> Url: https://github.com/boostorg/gil/pull/64  

### Description  
  
Minor severity: compiler warning clean up  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
### Environment  
  
All relevant information like:  
  
- Compiler version: MSVC 19.13.26129

---

## Review 1 [Commented]

> Username: chhenning  
> Created_at: 2018-03-27 12:24:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/64#pullrequestreview-107266007  

can we define y in the for loop? I cannot really see much in this code snippet.

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-03-27 12:30:58 UTC  
> Updated_at: 2018-03-27 12:31:10 UTC  
> Url: https://github.com/boostorg/gil/pull/64#issuecomment-376507214  

See the `y0<y` condition in the on-error clean up loop.  
  
```  
        typename View1::y_coord_t y = 0;  
        try {  
            for (y=0; y<view1.height(); ++y)  
                detail::uninitialized_copy_aux(view1.row_begin(y), view1.row_end(y),  
                                               view2.row_begin(y),  
                                               is_planar());  
        } catch(...) {  
            for (typename View1::y_coord_t y0=0; y0<y; ++y0)    <--- HERE  
                detail::destruct_aux(view2.row_begin(y0),view2.row_end(y0), is_planar());  
            throw;  
}  
```  
  
Doesn't it clean up successfully written rows only?  
Without carrying over the `y` value, how do you know how many rows to clean?  
  
So, can we? Depends if you know what are intentions of the original author. I'm not certain myself.

---

## Comment 3

> Username: chhenning  
> Created_at: 2018-03-27 12:36:27 UTC  
> Url: https://github.com/boostorg/gil/pull/64#issuecomment-376508735  

Correct. That part I couldn't see in the code snippet while reviewing. Let's merge!

---

## Comment 4

> Username: chhenning  
> Created_at: 2018-03-27 12:37:38 UTC  
> Url: https://github.com/boostorg/gil/pull/64#issuecomment-376509071  

You can also take out the `y=0` in the for loop. This way we might remove compiler warnings, etc.

---

## Comment 5

> Username: mloskot  
> Created_at: 2018-03-27 12:38:52 UTC  
> Updated_at: 2018-03-27 12:39:13 UTC  
> Url: https://github.com/boostorg/gil/pull/64#issuecomment-376509420  

> You can also take out the y=0 in the for loop.   
  
Then I would replace the `for` with `while` :) Let's leave it as in my patch.  
  
@chhenning  Do you want to merge?

---
