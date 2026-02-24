# #285 - Remove doc/doxygen/design_guide.dox as old docs remains [Closed]

> Username: mloskot  
> Created at: 2019-04-15 20:58:59 UTC  
> Updated at: 2019-04-15 21:17:17 UTC  
> Closed at: 2019-04-15 21:17:17 UTC  
> Url: https://github.com/boostorg/gil/issues/285  

In the [doc/doxygen](https://github.com/boostorg/gil/tree/33d4ac05ae9cef5705269658d1b7e007e825fbca/doc/doxygen), there are two files:  
  
- `design_guide.dox`  
- `tutorial.dox`  
  
@stefanseefeld   
Why they have been kept after migration to the new docs in reStructuredText?  
Shall we remove it?

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2019-04-15 21:00:12 UTC  
> Url: https://github.com/boostorg/gil/issues/285#issuecomment-483416740  

Could be an oversight. Yes, I think at this point doxygen should only cover the API reference guide. Everything else comes directly from sphinx.

---

## Comment 2

> Username: mloskot  
> Created at: 2019-04-15 21:14:23 UTC  
> Url: https://github.com/boostorg/gil/issues/285#issuecomment-483421529  

I compared content of the dox vs rst versions of both files and they are equivalent.  
I'm going to delete the `.dox` ones.
