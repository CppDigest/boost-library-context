# #208 - clang++-libc++ on Trusty doesn't seem to support thread_local [Closed]

> Username: pdimov  
> Created at: 2018-01-17 22:58:18 UTC  
> Updated at: 2018-01-29 17:35:15 UTC  
> Closed at: 2018-01-29 17:35:15 UTC  
> Url: https://github.com/boostorg/config/issues/208  

I added a few more configurations to Travis here: https://github.com/boostorg/config/commit/0ac815ed2158a9fc9c44c815af717b01c6ea72a7  
  
and the `clang++-libc++` one (which uses the provided `libc++-dev` package) fails when `thread_local` is used with  
  
```  
/home/travis/build/boostorg/boost-root/libs/config/test/boost_no_cxx11_thread_local.ipp:20: undefined reference to `__cxa_thread_atexit'  
```  
  
https://travis-ci.org/boostorg/config/jobs/329979139#L2474  
  
I'm not quite sure what we ought to do about that; either define `BOOST_NO_CXX11_THREAD_LOCAL` for libc++ on Linux, or somehow figure out a way to make that config work. I think I tend towards the former.  
  
(Having a working libc++ Linux Travis config is nice as it doesn't need to wait for the permanently clogged Mac jobs.)

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-01-18 10:16:40 UTC  
> Url: https://github.com/boostorg/config/issues/208#issuecomment-358601364  

I get the impression that libc++ on Linux is somewhat under-supported.  I've seen that error before - but can't remember where for now - and without using thread_local, just std lib code if I remember correctly.  
  
I believe the fix is to link to libcxxabi - but I've never found it that easy to set up on Linux - is there a package?

---

## Comment 2

> Username: pdimov  
> Created at: 2018-01-18 12:55:14 UTC  
> Url: https://github.com/boostorg/config/issues/208#issuecomment-358638444  

In this case everything else seems to work correctly, it's just `thread_local` that fails (and then presumably only in the case of a nontrivial destructor.)  
  
`smart_ptr` tests pass on this config: https://travis-ci.org/boostorg/smart_ptr/jobs/330025116  
and `system` passes too: https://travis-ci.org/boostorg/system/jobs/330093113  
  
So it's not like the whole ABI is missing; just `__cxa_thread_atexit`. Presumably `libsup++` gets linked in, why does its `__cxa_thread_exit` not work, no idea. The `libc++-dev` [package](https://packages.ubuntu.com/trusty/amd64/libc++-dev) contains no `libc++-abi.so`, just `libc++.so`, so that's not being used.  
  
The command `clang++-libc++` is just a shell script that calls `clang++ -stdlib=libc++` and nothing else; and `clang++` itself does support `thread_local` so it's somewhat of a mystery why `-stdlib=libc++` breaks it.  
  
No idea about `libcxxabi`. I generally try to keep the Travis configs as standard (as in, close to the system-provided) as possible, to mimic what real-world users would encounter.  
  
All in all, it seems to me that defining `BOOST_NO_CXX11_THREAD_LOCAL` for Linux `libc++` seems our best course at the moment.  
  
I haven't tried `g++-libc++` but no sane person would use that. :-)

---

## Comment 3

> Username: jzmaddock  
> Created at: 2018-01-18 18:43:14 UTC  
> Url: https://github.com/boostorg/config/issues/208#issuecomment-358742323  

OK lets go with that for now and see how it goes.

---

## Comment 4

> Username: pdimov  
> Created at: 2018-01-19 03:33:02 UTC  
> Url: https://github.com/boostorg/config/issues/208#issuecomment-358857239  

OK: https://github.com/boostorg/config/pull/210
