# #715 - Ready for 1.78? [Closed]

> Username: jzmaddock  
> Created at: 2021-11-02 19:18:40 UTC  
> Updated at: 2021-11-18 11:30:41 UTC  
> Closed at: 2021-11-18 11:30:40 UTC  
> Url: https://github.com/boostorg/math/issues/715  

Are there any show stoppers preventing a merge to master?  
  
@NAThompson hows the cubic_roots_test now?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-11-02 19:22:09 UTC  
> Url: https://github.com/boostorg/math/issues/715#issuecomment-958097534  

@NAThompson Ugh, always something: https://app.circleci.com/pipelines/github/boostorg/math/367/workflows/04c05dec-dbab-4cd3-af03-7355553cf84d/jobs/365

---

## Comment 2

> Username: NAThompson  
> Created at: 2021-11-02 19:28:40 UTC  
> Url: https://github.com/boostorg/math/issues/715#issuecomment-958101864  

@jzmaddock : Yup, my bad, will fix.

---

## Comment 3

> Username: NAThompson  
> Created at: 2021-11-02 19:29:26 UTC  
> Url: https://github.com/boostorg/math/issues/715#issuecomment-958102346  

BTW why do we care if there's non-ASCII characters in comments? If appears that every compiler handles it just fine.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2021-11-02 19:50:02 UTC  
> Url: https://github.com/boostorg/math/issues/715#issuecomment-958116294  

>BTW why do we care if there's non-ASCII characters in comments? If appears that every compiler handles it just fine.  
  
Sorry no idea what the rationale was.  I do know that users with far-eastern character sets can sometimes have all kinds of issues with non-ASCII characters, but I don't know the details....

---

## Comment 5

> Username: jzmaddock  
> Created at: 2021-11-03 08:59:24 UTC  
> Url: https://github.com/boostorg/math/issues/715#issuecomment-958757223  

OK, since we're up against the beta deadline (tomorrow!), I've done a merge to master, if we can fix the remaining CI failures in beta though that would be great.

---

## Comment 6

> Username: NAThompson  
> Created at: 2021-11-03 15:02:17 UTC  
> Updated at: 2021-11-03 15:02:29 UTC  
> Url: https://github.com/boostorg/math/issues/715#issuecomment-959358086  

@jzmaddock : Could you merge #716 for the beta?

---

## Comment 7

> Username: jzmaddock  
> Created at: 2021-11-03 15:32:48 UTC  
> Url: https://github.com/boostorg/math/issues/715#issuecomment-959461750  

> Could you merge #716 for the beta?  
  
Yep, I'll let the CI play out a little longer first... we have a few hours yet and it's nearly run through...

---

## Comment 8

> Username: jzmaddock  
> Created at: 2021-11-05 17:54:56 UTC  
> Url: https://github.com/boostorg/math/issues/715#issuecomment-962099325  

Can everyone please check that https://github.com/boostorg/math/commit/e000cf5dd1a9e02c322f5b094a8c057c6683b60c#diff-f4da1e74afb45ab98ca3a2ad30842e21a37b13a5956514b904889f941100f83e covers the release notes for this release?

---

## Comment 9

> Username: NAThompson  
> Created at: 2021-11-05 18:12:59 UTC  
> Updated at: 2021-11-05 18:14:44 UTC  
> Url: https://github.com/boostorg/math/issues/715#issuecomment-962111714  

@jzmaddock : Is there a spurious `std::cout` in `tanh_sinh_detail` in that commit?  
  
Also appears no mention of the Bezier polynomial support.

---

## Comment 10

> Username: jzmaddock  
> Created at: 2021-11-05 18:24:27 UTC  
> Url: https://github.com/boostorg/math/issues/715#issuecomment-962119248  

>  Is there a spurious std::cout in tanh_sinh_detail in that commit?  
  
Aaah, reverted.

---

## Comment 11

> Username: ckormanyos  
> Created at: 2021-11-05 18:55:32 UTC  
> Url: https://github.com/boostorg/math/issues/715#issuecomment-962138506  

> covers the release notes for this release?  
  
Hi @jzmaddock   
  
It's super trivial, but #507 has (think this is post-1.77):  
- Improve certain edge cases for `<cstdfloat>` complex-valued elementary functions.  
  
Cc: @mborland and @NAThompson

---

## Comment 12

> Username: jzmaddock  
> Created at: 2021-11-05 19:22:11 UTC  
> Url: https://github.com/boostorg/math/issues/715#issuecomment-962158512  

> Also appears no mention of the Bezier polynomial support.  
  
Done.  
  
>It's super trivial, but #507 has (think this is post-1.77):  
  
Also now done.

---

## Comment 13

> Username: NAThompson  
> Created at: 2021-11-06 15:36:07 UTC  
> Url: https://github.com/boostorg/math/issues/715#issuecomment-962468650  

Just read the docs for the cubic roots and for the Bezier polynomials, where are our new features for this release; they look good.  
  
Note that these updates are only on develop, not yet on master; is that expected?

---

## Comment 14

> Username: jzmaddock  
> Created at: 2021-11-06 16:52:42 UTC  
> Url: https://github.com/boostorg/math/issues/715#issuecomment-962478674  

>Note that these updates are only on develop, not yet on master; is that expected?  
  
The docs?  I've only just added them and master is currently closed for the Beta, once it re-opens we can merge the doc changes, plus whatever we need to get our CI clean again.

---

## Comment 15

> Username: jzmaddock  
> Created at: 2021-11-06 17:04:55 UTC  
> Url: https://github.com/boostorg/math/issues/715#issuecomment-962480338  

Hopefully this will green up CI: https://github.com/boostorg/math/commit/40605d0b78c397ceb337d1c427aa3e1d2a4803f6

---

## Comment 16

> Username: jzmaddock  
> Created at: 2021-11-18 11:30:40 UTC  
> Url: https://github.com/boostorg/math/issues/715#issuecomment-972781542  

Docs and history are merged, CI is green, closing down.
