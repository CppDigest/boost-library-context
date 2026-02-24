# #79 - Some section headers are hyperlinked [Open]

> Username: vinniefalco  
> Created at: 2021-09-08 17:37:46 UTC  
> Updated at: 2022-02-05 21:42:49 UTC  
> Url: https://github.com/boostorg/docca/issues/79  

Inexplicably, some section headers get hyperlinked. I have no idea why. This could be a Quickbook / Boostbook issue. Example:  
  
![image](https://user-images.githubusercontent.com/1503976/132557686-1c5bb9d5-9a18-41e6-a160-2d760d7ed919.png)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-02-04 19:22:54 UTC  
> Url: https://github.com/boostorg/docca/issues/79#issuecomment-1030282002  

"Synopsis", "Description BNF", and "See Also" are hyperlinked.  
  
This happens when Quickbook renders to HTML and produces an H7 or higher heading (HTML only supports H1 through H6)

---

## Comment 2

> Username: evanlenz  
> Created at: 2022-02-05 21:32:03 UTC  
> Url: https://github.com/boostorg/docca/issues/79#issuecomment-1030702287  

To address this issue, I have opened issue https://github.com/boostorg/boostbook/issues/12 and pull request https://github.com/boostorg/boostbook/pull/13.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-02-05 21:42:49 UTC  
> Url: https://github.com/boostorg/docca/issues/79#issuecomment-1030703830  

whoa !
