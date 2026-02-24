# #422 - add `swap` for `url` [Closed]

> Username: akrzemi1  
> Created at: 2022-08-17 18:42:07 UTC  
> Updated at: 2022-09-01 22:56:39 UTC  
> Closed at: 2022-09-01 22:56:39 UTC  
> Url: https://github.com/boostorg/url/issues/422  

It looks like `url` is missing a `swap`. It could be more efficient than using `std::swap`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-17 18:43:59 UTC  
> Url: https://github.com/boostorg/url/issues/422#issuecomment-1218370252  

Oh... all the swaps are missing... I think I might have removed them when I did the big refactoring and forgotten to add them back.
