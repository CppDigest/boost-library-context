# #431 - Compilation failure using numpy 2.0.0b1 [Closed]

> Username: duburcqa  
> Created at: 2024-03-15 07:05:44 UTC  
> Updated at: 2024-08-19 08:39:03 UTC  
> Closed at: 2024-05-07 17:04:38 UTC  
> Url: https://github.com/boostorg/python/issues/431  

I tried to compile Boost::Python 1.84 library against `numpy==2.0.0b1`, but I'm getting a compilation error:  
```  
libs/python/src/numpy/dtype.cpp:101:83: error: no member named 'elsize' in '_PyArray_Descr'  
int dtype::get_itemsize() const { return reinterpret_cast<PyArray_Descr*>(ptr())->elsize;}  
```

---

## Comment 1

> Username: duburcqa  
> Created at: 2024-03-15 07:10:04 UTC  
> Updated at: 2024-03-15 07:16:33 UTC  
> Url: https://github.com/boostorg/python/issues/431#issuecomment-1999061713  

See related part of numpy documentation: https://numpy.org/devdocs/release/2.0.0-notes.html#descriptor-elsize-and-alignment-access  
  
The new accessor `PyDataType_ELSIZE` must be used: `PyDataType_ELSIZE(reinterpret_cast<PyArray_Descr*>(ptr()))`

---

## Comment 2

> Username: jakirkham  
> Created at: 2024-05-07 16:58:41 UTC  
> Url: https://github.com/boostorg/python/issues/431#issuecomment-2098906592  

Was this fixed with PR ( https://github.com/boostorg/python/pull/432 )?

---

## Comment 3

> Username: duburcqa  
> Created at: 2024-05-07 17:04:38 UTC  
> Url: https://github.com/boostorg/python/issues/431#issuecomment-2098916743  

Yes. Closing.

---

## Comment 4

> Username: jakirkham  
> Created at: 2024-05-07 17:44:00 UTC  
> Url: https://github.com/boostorg/python/issues/431#issuecomment-2098981168  

Thanks Alexis! 🙏

---

## Comment 5

> Username: jakobandersen  
> Created at: 2024-08-19 07:54:44 UTC  
> Url: https://github.com/boostorg/python/issues/431#issuecomment-2295902168  

While fixed now, it doesn't seem possible to build any prior Boost release if NumPy 2.x is installed. Is it possible to disable just the NumPy part when building via the central ``b2``?

---

## Comment 6

> Username: jakirkham  
> Created at: 2024-08-19 08:18:27 UTC  
> Url: https://github.com/boostorg/python/issues/431#issuecomment-2295953680  

[Boost 1.86.0]( https://www.boost.org/users/history/version_1_86_0.html ) got released just a few days ago. Did this not make it in there?

---

## Comment 7

> Username: jakobandersen  
> Created at: 2024-08-19 08:29:32 UTC  
> Url: https://github.com/boostorg/python/issues/431#issuecomment-2295977920  

> [Boost 1.86.0](https://www.boost.org/users/history/version_1_86_0.html) got released just a few days ago. Did this not make it in there?  
  
It doesn't look like it :-(

---

## Comment 8

> Username: jakirkham  
> Created at: 2024-08-19 08:39:02 UTC  
> Url: https://github.com/boostorg/python/issues/431#issuecomment-2295999103  

@stefanseefeld what do we need to do to get this in the next Boost release? Is 1.86.1 an option?
