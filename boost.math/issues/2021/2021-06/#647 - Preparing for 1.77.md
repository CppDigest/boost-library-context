# #647 - Preparing for 1.77 [Closed]

> Username: jzmaddock  
> Created at: 2021-06-25 18:18:12 UTC  
> Updated at: 2021-07-17 17:56:39 UTC  
> Closed at: 2021-07-17 17:56:39 UTC  
> Url: https://github.com/boostorg/math/issues/647  

I see the next release cycle has come around again, any reason not to merge for 1.77, anything we need to do?  
  
@NAThompson , @pabristow , @mborland  ?

---

## Comment 1

> Username: mborland  
> Created at: 2021-06-25 18:26:33 UTC  
> Url: https://github.com/boostorg/math/issues/647#issuecomment-868753736  

The only bug fix I have that should make it is https://github.com/boostorg/math/pull/648.

---

## Comment 2

> Username: NAThompson  
> Created at: 2021-06-25 19:56:19 UTC  
> Updated at: 2021-06-25 19:56:33 UTC  
> Url: https://github.com/boostorg/math/issues/647#issuecomment-868800852  

Looks like #646 will also be necessary. Other than that nothing from me.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2021-06-26 08:43:32 UTC  
> Url: https://github.com/boostorg/math/issues/647#issuecomment-868970221  

I did not make as much progress as hoped on `<cstdfloat.hpp>` in #506. It's too late for significant progress now. So I'll need to push that off till 1.78.

---

## Comment 4

> Username: pabristow  
> Created at: 2021-06-26 08:52:27 UTC  
> Url: https://github.com/boostorg/math/issues/647#issuecomment-868971257  

We definitely should merge PR to correct the error in hypergeometric distribution kurtosis. Hypergeomkurtosis #642.  I was sort or waiting for a review, but will do it today.
