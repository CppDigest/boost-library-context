# #356 - FindHana.cmake is not there anymore, but the tutorial still mentions it [Closed]

> Username: ldionne  
> Created at: 2017-06-29 02:33:50 UTC  
> Updated at: 2017-07-01 19:53:22 UTC  
> Closed at: 2017-07-01 17:33:23 UTC  
> Url: https://github.com/boostorg/hana/issues/356  

Thanks to Michael Park for the notice.

---

## Comment 1

> Username: ldionne  
> Created at: 2017-07-01 17:33:23 UTC  
> Url: https://github.com/boostorg/hana/issues/356#issuecomment-312445241  

Actually, it turns out that this was only because I merged `develop` into `master` and it took some time to regenerate the documentation. I had correctly fixed the documentation when I moved to modern CMake dependency management (using `HanaConfig.cmake` in d53ef246). @mpark just happened to look just at the wrong moment!

---

## Comment 2

> Username: mpark  
> Created at: 2017-07-01 19:53:22 UTC  
> Url: https://github.com/boostorg/hana/issues/356#issuecomment-312452419  

😅
