# #2 - [Doc] Still using old MPLLIBS_STRING macro in the tutorial [Closed]

> Username: ldionne  
> Created at: 2015-06-03 21:01:07 UTC  
> Updated at: 2025-05-03 05:00:02 UTC  
> Closed at: 2025-05-03 05:00:02 UTC  
> Url: https://github.com/boostorg/metaparse/issues/2  

The tutorial is still using the `MPLLIBS_STRING` and `MPLLIBS_DEFINE_ERROR` macros instead of their `BOOST_xxx` counterpart. This should be fixed if the library makes it in.

---

## Comment 1

> Username: sabel83  
> Created at: 2015-06-03 21:29:24 UTC  
> Url: https://github.com/boostorg/metaparse/issues/2#issuecomment-108621093  

Yes, and it looks like the diagrams need to be updated as well. I'll fix these after the review (once the xxx part of the BOOST_xxx names have been chosen :))
