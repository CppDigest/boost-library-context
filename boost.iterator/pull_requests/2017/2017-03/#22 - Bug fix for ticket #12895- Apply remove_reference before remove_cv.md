# #22 Bug fix for ticket #12895: Apply remove_reference before remove_cv [Merged]

> Username: Dani-Hub  
> Created at: 2017-03-14 19:49:22 UTC  
> Updated at: 2017-03-16 00:08:53 UTC  
> Merged at: 2017-03-15 16:12:13 UTC  
> Closed at: 2017-03-15 16:12:13 UTC  
> Url: https://github.com/boostorg/iterator/pull/22  

The suggested code change ensures that the zip_iterator dereference also works for Standard Libraries where iterator_traits are not SFINAE-friendly (e.g. Visual Studio 2012)

---

## Comment 1

> Username: eldiener  
> Created_at: 2017-03-15 12:17:46 UTC  
> Url: https://github.com/boostorg/iterator/pull/22#issuecomment-286724849  

I am looking at this and will merge as soon as I am satisfied that this change fixes the problem and causes no further regressions.

---

## Comment 2

> Username: morinmorin  
> Created_at: 2017-03-16 00:08:53 UTC  
> Url: https://github.com/boostorg/iterator/pull/22#issuecomment-286918928  

Thanks for merging!

---
