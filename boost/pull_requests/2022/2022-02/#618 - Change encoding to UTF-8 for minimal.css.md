# #618 Change encoding to UTF-8 for minimal.css [Merged]

> Username: jwakely  
> Created at: 2022-02-03 01:06:29 UTC  
> Updated at: 2023-01-19 15:19:49 UTC  
> Merged at: 2023-01-19 15:10:23 UTC  
> Closed at: 2023-01-19 15:10:23 UTC  
> Url: https://github.com/boostorg/boost/pull/618  

CSS files should be UTF-8: https://www.w3.org/International/questions/qa-css-charset

---

## Comment 1

> Username: jwakely  
> Created_at: 2022-02-03 01:11:28 UTC  
> Url: https://github.com/boostorg/boost/pull/618#issuecomment-1028509584  

https://github.com/boostorg/filesystem/commit/9df2bfab58aadf35b4e01f2fd6252e1521409779#diff-36873c6a5a238ba4df45a47d97d80ead5ed664b3cb7e5a8fbe918020cc6889a4 replaced another ISO-8859-1 copyright symbol with `(c)`, which would also be an option here. I just want to get rid of the ISO-8859-1 character in a file that doesn't specify its `@charset`, because it causes warnings for distro packagers.

---

## Comment 2

> Username: mclow  
> Created_at: 2022-02-03 03:35:09 UTC  
> Url: https://github.com/boostorg/boost/pull/618#issuecomment-1028571203  

I think changing to '(c)' is the simplest fix.

---

## Comment 3

> Username: jwakely  
> Created_at: 2022-02-03 08:14:03 UTC  
> Url: https://github.com/boostorg/boost/pull/618#issuecomment-1028710447  

OK thanks, I'll re-push with that change then...

---

## Comment 4

> Username: jwakely  
> Created_at: 2022-02-03 17:57:56 UTC  
> Url: https://github.com/boostorg/boost/pull/618#issuecomment-1029251868  

Now done consistently with https://github.com/boostorg/detail/commit/8c603a1894ec1d8abc7a0a538297923b0ac98b33

---

## Comment 5

> Username: jwakely  
> Created_at: 2022-06-14 16:15:22 UTC  
> Url: https://github.com/boostorg/boost/pull/618#issuecomment-1155412329  

Ping

---
