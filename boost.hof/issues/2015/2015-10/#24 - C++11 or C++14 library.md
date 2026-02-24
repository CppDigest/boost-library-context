# #24 - C++11 or C++14 library? [Closed]

> Username: viboes  
> Created at: 2015-10-04 00:38:06 UTC  
> Updated at: 2015-10-30 22:49:47 UTC  
> Closed at: 2015-10-30 22:49:47 UTC  
> Url: https://github.com/boostorg/hof/issues/24  

In http://fit.readthedocs.org/en/latest/index.html I  can see  
"Fit is a header-only C++11 library"  
" Fit takes advantages of modern C++11/C++14 features"  
  
Does Fit need some C++14 support? If suspect that not because it is a C++11library.  
  
This is confusing.

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 04:05:59 UTC  
> Url: https://github.com/boostorg/hof/issues/24#issuecomment-145425217  

The minimum requirement is C++11, but there are a few things that require C++14(like [`FIT_LIFT`](http://fit.readthedocs.org/en/latest/lift/index.html)).

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-05 22:51:58 UTC  
> Url: https://github.com/boostorg/hof/issues/24#issuecomment-145691328  

I have no problem with that.  This must however be clarified.

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-30 22:49:47 UTC  
> Url: https://github.com/boostorg/hof/issues/24#issuecomment-152668661  

This is updated in the latest updates to the docs.
