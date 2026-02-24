# #502 - Allow reseating reference types? [Closed]

> Username: vinniefalco  
> Created at: 2022-09-04 15:44:16 UTC  
> Updated at: 2022-09-11 23:35:39 UTC  
> Closed at: 2022-09-11 23:35:39 UTC  
> Url: https://github.com/boostorg/url/issues/502  

params and segments views can be reseated with `operator=` but this is weird, because it looks like container elements assignment. Should we remove it?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-11 23:35:39 UTC  
> Url: https://github.com/boostorg/url/issues/502#issuecomment-1243068365  

reseating is required for const views and becomes assignment for mutable views, this is done.
