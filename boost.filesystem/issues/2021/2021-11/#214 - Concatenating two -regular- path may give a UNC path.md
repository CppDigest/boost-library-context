# #214 - Concatenating two "regular" path may give a UNC path [Closed]

> Username: ypf791  
> Created at: 2021-11-03 07:22:07 UTC  
> Updated at: 2021-11-04 23:22:14 UTC  
> Closed at: 2021-11-04 23:22:14 UTC  
> Url: https://github.com/boostorg/filesystem/issues/214  

`cout << path{"/"} / path{"/haha"} << endl`  
  
This prints "//haha", which is a UNC path, but I expect it to print "/haha".

---

## Comment 1

> Username: Lastique  
> Created at: 2021-11-04 00:20:02 UTC  
> Url: https://github.com/boostorg/filesystem/issues/214#issuecomment-960315418  

This is expected behavior on Filesystem v3. Worth changing in v4.
