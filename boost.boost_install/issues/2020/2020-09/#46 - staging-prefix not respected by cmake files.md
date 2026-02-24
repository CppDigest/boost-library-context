# #46 - staging-prefix not respected by cmake files [Open]

> Username: rossburton  
> Created at: 2020-09-29 16:28:45 UTC  
> Updated at: 2020-09-29 16:54:37 UTC  
> Url: https://github.com/boostorg/boost_install/issues/46  

If I install Boost with `b2 install --prefix=/usr staging-prefix=/tmp/stage/usr` the libraries are installed to `/tmp/stage/usr/lib/...` but fails as the the cmake files are not relocated under the staging prefix:  
  
```  
common.mkdir /usr/lib/cmake  
| mkdir: cannot create directory ‘/usr/lib/cmake’: Permission denied  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2020-09-29 16:50:36 UTC  
> Url: https://github.com/boostorg/boost_install/issues/46#issuecomment-700842652  

`staging-prefix` is a new feature, added in https://github.com/boostorg/build/commit/51ad47134a2791599b2ce5604fab7da1e877c5c1. It's not clear how these new features are supposed to interact with the old command-line options (`--prefix`, `--libdir` etc) used when installing Boost.

---

## Comment 2

> Username: pdimov  
> Created at: 2020-09-29 16:53:06 UTC  
> Url: https://github.com/boostorg/boost_install/issues/46#issuecomment-700844082  

Other than that, the functionality has already been requested in the form of `DESTDIR` support, which I was going to implement as a `--destdir` option, but `--staging-prefix` works too, I guess. https://github.com/boostorg/boost_install/issues/34

---

## Comment 3

> Username: rossburton  
> Created at: 2020-09-29 16:54:37 UTC  
> Url: https://github.com/boostorg/boost_install/issues/46#issuecomment-700844934  

My impression was that `staging-prefix` is basically `DESTDIR`, but if you're going to add proper `DESTDIR` semantics then that would be better.
