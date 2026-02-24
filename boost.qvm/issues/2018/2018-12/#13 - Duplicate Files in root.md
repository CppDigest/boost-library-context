# #13 - Duplicate Files in root [Closed]

> Username: teeks99  
> Created at: 2018-12-13 01:33:05 UTC  
> Updated at: 2018-12-17 22:29:58 UTC  
> Closed at: 2018-12-17 22:29:58 UTC  
> Url: https://github.com/boostorg/qvm/issues/13  

There are two files, " README.adoc" and "README.adoc" in the [source repo](https://github.com/boostorg/qvm).  When these are extracted on windows, the leading space on the first file is dropped. This causes the two files to try to have the same name, prompting one to try to overwrite the other.  
  
Also posted to the boost-users list.

---

## Comment 1

> Username: mwpowellhtx  
> Created at: 2018-12-13 01:42:16 UTC  
> Url: https://github.com/boostorg/qvm/issues/13#issuecomment-446811031  

Thanks for the verification on that one. It's not a show stopper, I think; I let the extraction "rename" one of the offending duplicates. Still, I doubt it should be a duplicate as such.

---

## Comment 2

> Username: zajo  
> Created at: 2018-12-17 22:29:58 UTC  
> Url: https://github.com/boostorg/qvm/issues/13#issuecomment-448023577  

Thanks, this is fixed now.
