# #78 - Formatting issue in documentation [Closed]

> Username: yunesj  
> Created at: 2016-07-21 23:57:03 UTC  
> Updated at: 2016-08-07 05:28:22 UTC  
> Closed at: 2016-08-07 05:28:22 UTC  
> Url: https://github.com/boostorg/python/issues/78  

In this [section](http://www.boost.org/doc/libs/1_61_0/libs/python/doc/html/tutorial/tutorial/techniques.html) with text:  
  
> The filename will have to be changed to _core.pyd  
  
It looks like the underscores were replaced with underlying. Image attached.

---

## Comment 1

> Username: yunesj  
> Created at: 2016-07-21 23:57:16 UTC  
> Url: https://github.com/boostorg/python/issues/78#issuecomment-234418886  

![screen shot 2016-07-21 at 4 53 45 pm](https://cloud.githubusercontent.com/assets/152988/17042644/2a7859d6-4f64-11e6-8f0b-ed31d58dafad.png)

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2016-08-07 05:28:22 UTC  
> Url: https://github.com/boostorg/python/issues/78#issuecomment-238064597  

Thanks for the report. This is fixed in https://github.com/boostorg/python/commit/94dfa4c7625398ad30f2f503ac1eeb151dc8cebb and should percolate to the online documentation soon. (See http://boostorg.github.io/python/doc/html/tutorial/tutorial/techniques.html#tutorial.techniques.creating_packages).
