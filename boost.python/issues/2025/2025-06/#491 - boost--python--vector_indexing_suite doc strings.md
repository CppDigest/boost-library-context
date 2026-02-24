# #491 - boost::python::vector_indexing_suite doc strings [Open]

> Username: CEXT-Dan  
> Created at: 2025-06-09 09:23:00 UTC  
> Updated at: 2025-06-09 09:23:00 UTC  
> Url: https://github.com/boostorg/python/issues/491  

Hi,   
  
in my [project](https://github.com/CEXT-Dan/PyRx), I generate `.pyi` files from doc strings, but I'm unsure how to add my doc strings to `boost::python::vector_indexing_suite`, specifically to `append `and `extend` as base_append and base_extend are private, help a newb : )  
  
  
https://github.com/CEXT-Dan/PyRx/blob/d9fe3006695ae2866754d0177cafd3fc59f328e0/PyRxCore/PyDbObjectId.h#L47  
https://github.com/CEXT-Dan/PyRx/blob/d9fe3006695ae2866754d0177cafd3fc59f328e0/PyRxCore/PyDbObjectId.cpp#L13-L14
