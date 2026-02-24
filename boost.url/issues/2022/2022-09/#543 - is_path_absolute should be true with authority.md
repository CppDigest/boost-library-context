# #543 - is_path_absolute should be true with authority [Closed]

> Username: vinniefalco  
> Created at: 2022-09-13 21:04:10 UTC  
> Updated at: 2022-09-20 17:58:35 UTC  
> Closed at: 2022-09-20 17:58:35 UTC  
> Url: https://github.com/boostorg/url/issues/543  

When an authority is present, `is_path_absolute` should always return `true`.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-09-13 22:16:47 UTC  
> Url: https://github.com/boostorg/url/issues/543#issuecomment-1246004918  

Related issue: #460

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-09-20 17:58:35 UTC  
> Url: https://github.com/boostorg/url/issues/543#issuecomment-1252713086  

We decided against this, because it makes things inconsistent
