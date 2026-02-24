# #22 Change encoding to UTF-8 for doc/html/minimal.css [Closed]

> Username: jwakely  
> Created at: 2022-02-03 01:18:58 UTC  
> Updated at: 2022-02-03 09:18:47 UTC  
> Closed at: 2022-02-03 09:01:00 UTC  
> Url: https://github.com/boostorg/detail/pull/22  

CSS files should be UTF-8: https://www.w3.org/International/questions/qa-css-charset  
  
https://github.com/boostorg/filesystem/commit/9df2bfab58aadf35b4e01f2fd6252e1521409779#diff-36873c6a5a238ba4df45a47d97d80ead5ed664b3cb7e5a8fbe918020cc6889a4 replaced another ISO-8859-1 copyright symbol with `(c)`, which would also be an option here. I just want to get rid of the ISO-8859-1 character in a file that doesn't specify its `@charset`, because it causes warnings for distro packagers.  
  
See also https://github.com/boostorg/boost/pull/618 and https://github.com/boostorg/multi_index/pull/55

---

## Comment 1

> Username: Lastique  
> Created_at: 2022-02-03 09:01:31 UTC  
> Url: https://github.com/boostorg/detail/pull/22#issuecomment-1028756932  

Thanks for the report, I applied a slightly different fix.

---

## Comment 2

> Username: jwakely  
> Created_at: 2022-02-03 09:18:47 UTC  
> Url: https://github.com/boostorg/detail/pull/22#issuecomment-1028770966  

Great, thanks! Marshall suggested using (c) elsewhere too, so I'll adjust my other pull requests to do that.

---
