# #400 - WebAssembly and BOOST_HAS_UNISTD_H [Closed]

> Username: dmateja  
> Created at: 2021-08-25 13:12:14 UTC  
> Updated at: 2021-09-10 10:37:01 UTC  
> Closed at: 2021-09-10 10:37:00 UTC  
> Url: https://github.com/boostorg/config/issues/400  

Hello  
In 1.77.0 I can't compile filesystem by Emscripten because of missing open/close file functions.  
It looks like missing BOOST_HAS_UNISTD_H defined for wasm platform.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-09-10 10:37:00 UTC  
> Url: https://github.com/boostorg/config/issues/400#issuecomment-916806034  

Fixed in https://github.com/boostorg/config/pull/403
