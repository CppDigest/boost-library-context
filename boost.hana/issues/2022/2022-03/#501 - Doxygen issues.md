# #501 - Doxygen issues [Closed]

> Username: beojan  
> Created at: 2022-03-24 18:49:30 UTC  
> Updated at: 2022-10-21 03:43:49 UTC  
> Closed at: 2022-10-21 03:43:49 UTC  
> Url: https://github.com/boostorg/hana/issues/501  

There are a few places where the Doxygen documentation appears to be processed incorrectly. For example, in `Functional`, a function called `mathtt` is listed, which seems to contain the documentation for multiple functions. Similarly, `MonadPlus` has a `mathrm` listed.

---

## Comment 1

> Username: ldionne  
> Created at: 2022-10-21 03:43:49 UTC  
> Url: https://github.com/boostorg/hana/issues/501#issuecomment-1286419154  

Thanks for reporting. Duping to #483.  
  
The fundamental problem is that Doxygen is kind of broken. I just tried to fix some of the issues and failed, I think we may have to rewrite the documentation using a different tool. I remember I had stretched Doxygen to its limit when I wrote it initially, and maybe that wasn't a great idea in retrospect.
