# #43 Change encoding to UTF-8 for doc/styles.css [Open]

> Username: jwakely  
> Created at: 2022-02-03 01:21:01 UTC  
> Updated at: 2025-01-22 18:47:39 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/43  

CSS files should be UTF-8: https://www.w3.org/International/questions/qa-css-charset  
  
https://github.com/boostorg/filesystem/commit/9df2bfab58aadf35b4e01f2fd6252e1521409779#diff-36873c6a5a238ba4df45a47d97d80ead5ed664b3cb7e5a8fbe918020cc6889a4 replaced another ISO-8859-1 copyright symbol with `(c)`, which would also be an option here. I just want to get rid of the ISO-8859-1 character in a file that doesn't specify its `@charset`, because it causes warnings for distro packagers.  
  
See also https://github.com/boostorg/boost/pull/618 and https://github.com/boostorg/multi_index/pull/55 and https://github.com/boostorg/detail/pull/22

---

## Comment 1

> Username: jwakely  
> Created_at: 2022-06-14 16:25:54 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/43#issuecomment-1155423409  

Ping  
  
Would you prefer to do this consistently with https://github.com/boostorg/boost/pull/618 and https://github.com/boostorg/multi_index/pull/55 and https://github.com/boostorg/detail/pull/22 i.e. "Copyright (c) YYYY, Copyright Holder" ?

---

## Comment 2

> Username: jwakely  
> Created_at: 2022-06-14 16:46:11 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/43#issuecomment-1155446834  

I've updated the PR that way now.

---

## Comment 3

> Username: e-kwsm  
> Created_at: 2025-01-22 16:39:10 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/43#issuecomment-2607733172  

FWIW: HTML files, e.g. index.html, are not UTF8.

---

## Comment 4

> Username: jwakely  
> Created_at: 2025-01-22 17:57:10 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/43#issuecomment-2607904413  

> FWIW: HTML files, e.g. index.html, are not UTF8.  
  
The patch is for a CSS file, so I'm not sure what you're saying here, or if it matters to this PR.

---

## Comment 5

> Username: e-kwsm  
> Created_at: 2025-01-22 18:47:37 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/43#issuecomment-2608011717  

#61 supersedes this.

---
