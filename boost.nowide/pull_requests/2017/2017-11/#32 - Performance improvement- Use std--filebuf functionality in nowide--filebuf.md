# #32 Performance improvement:  Use std::filebuf functionality in nowide::filebuf [Closed]

> Username: EvanBalster  
> Created at: 2017-11-03 21:55:45 UTC  
> Updated at: 2019-11-08 18:23:15 UTC  
> Closed at: 2017-11-05 05:58:08 UTC  
> Url: https://github.com/boostorg/nowide/pull/32  

These changes replace `nowide`'s `filebuf` type with a simpler class that extends `std::filebuf`.  This enables buffered reading and writing, resulting in extreme (>99%) reductions in read/write time for large I/O operations.  
  
`test_fstream.cpp` currently fails in "complex IO" tests with this code, but I suspect this may relate to a sleeper issue with `seek` functions unrelated to the changes presented here.  Requesting developer review.

---

## Comment 1

> Username: EvanBalster  
> Created_at: 2017-11-03 22:05:11 UTC  
> Url: https://github.com/boostorg/nowide/pull/32#issuecomment-341838780  

See the instigating issue:  https://github.com/artyom-beilis/nowide/issues/31

---

## Comment 2

> Username: artyom-beilis  
> Created_at: 2017-11-05 05:58:08 UTC  
> Url: https://github.com/boostorg/nowide/pull/32#issuecomment-341951125  

I can't integrate this changeset since it uses `std::filebuf::open(wchar_t const *)`  - it is private MSVC extension and is not available in other implementations like MinGW32.

---

## Comment 3

> Username: Flamefire  
> Created_at: 2019-11-08 18:23:14 UTC  
> Url: https://github.com/boostorg/nowide/pull/32#issuecomment-551937328  

I may have an acceptable solution: MSVC supports `filebuf::filebuf(FILE*)` and Mingw32 has `filebuf::_M_file::sys_open(__c_file*, ios_base::openmode)`. Are there any other windows-compatible stdlibs?  
  
If this covers all, then one can open a FILE* with nowide, pass it to either of the underlying filebuf classes and let them handle the rest. This gives us performance and conformance for (almost) free. E.g. Nowide is missing the conversion stuff (codecvt etc. on reading non-binary files)

---
