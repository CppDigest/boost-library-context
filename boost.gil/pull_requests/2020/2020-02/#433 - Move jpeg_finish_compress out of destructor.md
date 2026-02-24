# #433 Move jpeg_finish_compress out of destructor [Merged]

> Username: simmplecoder  
> Created at: 2020-02-14 11:21:45 UTC  
> Updated at: 2020-02-19 07:18:16 UTC  
> Merged at: 2020-02-19 07:03:38 UTC  
> Closed at: 2020-02-19 07:03:38 UTC  
> Url: https://github.com/boostorg/gil/pull/433  

### Description  
  
This pull request removes the `jpeg_finish_compress` call from destructor, which will ensure no erroring function can be called when an exception is in flight or the destructor can fail. If we would throw out of destructor some time in the future, we will have to put `noexcept(false)`, as destructors are `noexcept` by default.  
  
Thanks a lot to [@misos1](https://github.com/misos1) for having productive discussion about the `setjmp` and `longjmp` in the [original issue](https://github.com/boostorg/gil/issues/416).  
  
### References  
  
#416   
  
### Tasklist  
  
- [x] Add test case(s) - not applicable ?  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: simmplecoder  
> Created_at: 2020-02-14 11:23:35 UTC  
> Url: https://github.com/boostorg/gil/pull/433#issuecomment-586218971  

I've lurked around and haven't spotted resource leak. Could anyone please confirm this? This commit should deal with both undefined behavior, termination and resource leakage if that's right.

---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2020-02-14 19:18:20 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/433#pullrequestreview-359155978  

LGTM!  
  
I think it is a good idea to add comment in `~writer_backend()`, something like:  
  
```  
// JPEG compression object destruction does not signal errors,  
// unlike jpeg_finish_compress called elsewhere,  
// so there is no need for the setjmp bookmark here.  
jpeg_destroy_compress(get());  
```  
  
This should help a suspicious reader to avoid an urge to patch it :)

📁 include/boost/gil/extension/io/jpeg/detail/write.hpp

```diff
 135 |+ 
 136 |+         jpeg_finish_compress ( this->get() );
 137 |+ 
```

> Username: mloskot  
> Created_at: 2020-02-14 19:05:30 UTC  
> Updated_at: 2020-02-15 06:41:26 UTC  
> Url: https://github.com/boostorg/gil/pull/433#discussion_r379595651  

Nitpick: let's try to avoid superfluous blanks 😛


---

## Comment 3

> Username: simmplecoder  
> Created_at: 2020-02-15 06:43:52 UTC  
> Url: https://github.com/boostorg/gil/pull/433#issuecomment-586560803  

@mloskot , thanks! I did as you mentioned, and I believe the `setjmp`/`longjmp`  issues are at least not that critical now. There might be resource leaks somewhere, but otherwise no undefined behavior should occur if the C library underneath errored out.

---

## Review 4 [Approved]

> Username: mloskot  
> Created_at: 2020-02-15 09:15:45 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/433#pullrequestreview-359336051  

Thanks. Many thanks to @misos1 for his involvement and help

---

## Comment 5

> Username: mloskot  
> Created_at: 2020-02-19 07:18:16 UTC  
> Url: https://github.com/boostorg/gil/pull/433#issuecomment-588071603  

Awesome. Thanks again @simmplecoder & @misos1 for your efforts.

---
