# #30 - How FIT_STATIC_FUNCTION is related to static_const from Eric? [Closed]

> Username: viboes  
> Created at: 2015-10-04 01:00:10 UTC  
> Updated at: 2015-10-31 00:35:46 UTC  
> Closed at: 2015-10-30 22:51:45 UTC  
> Url: https://github.com/boostorg/hof/issues/30  



---

## Comment 1

> Username: viboes  
> Created at: 2015-10-04 01:02:49 UTC  
> Url: https://github.com/boostorg/hof/issues/30#issuecomment-145303839  

Has Boost.Hana something related?  
  
The same question for `FIT_STATIC_LAMBDA`

---

## Comment 2

> Username: ldionne  
> Created at: 2015-10-04 01:05:37 UTC  
> Url: https://github.com/boostorg/hof/issues/30#issuecomment-145303922  

No, Hana does not have anything similar to `FIT_STATIC_FUNCTION`.

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-05 04:23:52 UTC  
> Url: https://github.com/boostorg/hof/issues/30#issuecomment-145427052  

On compilers that support it, it uses the `static_const` technique from Eric Niebler. In the future, hopefully, it will use an inline variable instead. `FIT_STATIC_LAMBDA` and `FIT_STATIC_LAMBDA_FUNCTION` are not related to Eric Niebler's `static_const`. I wrote a blog post about it [here](http://pfultz2.com/blog/2015/05/31/unique-address/) that goes into more detail about some the techniques used.

---

## Comment 4

> Username: viboes  
> Created at: 2015-10-05 22:47:49 UTC  
> Url: https://github.com/boostorg/hof/issues/30#issuecomment-145690694  

I believe that then you can describe it, FIT_STATIC_FUNCTION should refer to the Eric proposal and FIT_STATIC_LAMBDA_FUNCTION should refer to your post.

---

## Comment 5

> Username: pfultz2  
> Created at: 2015-10-30 22:51:45 UTC  
> Url: https://github.com/boostorg/hof/issues/30#issuecomment-152668901  

I added links to Eric Niebler's n4381.

---

## Comment 6

> Username: viboes  
> Created at: 2015-10-31 00:35:46 UTC  
> Url: https://github.com/boostorg/hof/issues/30#issuecomment-152680099  

Have add a link to your blog?
