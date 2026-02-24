# #328 - Error in directory.cpp when cross-compiling for older FreeBSD [Closed]

> Username: alexsharoff  
> Created at: 2024-10-03 09:15:24 UTC  
> Updated at: 2024-10-03 10:47:54 UTC  
> Closed at: 2024-10-03 10:47:35 UTC  
> Url: https://github.com/boostorg/filesystem/issues/328  

`dirfd` is a macro on FreeBSD 8, so compiling the following code fails with `error: expected id-expression before '(' token`:  
  
https://github.com/boostorg/filesystem/blob/fbd23ee0e07ff17d486534314f89d1b5fb306824/src/directory.cpp#L273

---

## Comment 1

> Username: Lastique  
> Created at: 2024-10-03 10:47:53 UTC  
> Url: https://github.com/boostorg/filesystem/issues/328#issuecomment-2391101356  

Thanks for the report.
