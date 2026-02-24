# #75 - Undefined behavior due to use of *_impl pointers [Closed]

> Username: masterleinad  
> Created at: 2017-11-05 22:42:59 UTC  
> Updated at: 2017-11-08 17:20:04 UTC  
> Closed at: 2017-11-08 17:20:04 UTC  
> Url: https://github.com/boostorg/serialization/issues/75  

Compiling with `-fsanitize=undefined` gives a lot of errors similar to  
```  
 testing.capture-output ../../../bin.v2/libs/serialization/test/test_class_info_save_xml_warchive.test/gcc-gnu-7.2.0/debug/threadapi-pthread/                            test_class_info_save_xml_warchive.run  
 ====== BEGIN OUTPUT ======  
 ../../../boost/archive/detail/interface_oarchive.hpp:47:16: runtime error: downcast of address 0x7ffd66f64220 which does not point to an object of type 'xml_woarchive'  
 0x7ffd66f64220: note: object is of type 'boost::archive::xml_woarchive_impl<boost::archive::xml_woarchive>'  
  00 00 00 00  50 12 c7 4f f2 7f 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
               ^~~~~~~~~~~~~~~~~~~~~~~  
               vptr for 'boost::archive::xml_woarchive_impl<boost::archive::xml_woarchive>'  
 ../../../boost/archive/iterators/wchar_from_mb.hpp:168:31: runtime error: member call on address 0x7ffd66f63cf0 which does not point to an object of type               '__codecvt_abstract_base'  
 0x7ffd66f63cf0: note: object is of type 'boost::archive::detail::utf8_codecvt_facet'  
  f2 7f 00 00  48 25 8d 4f f2 7f 00 00  00 00 00 00 00 00 00 00  60 1b b8 4d f2 7f 00 00  00 00 00 00  
               ^~~~~~~~~~~~~~~~~~~~~~~  
               vptr for 'boost::archive::detail::utf8_codecvt_facet'  
 /opt/compiler/gcc-7.2.0/include/c++/7.2.0/bits/codecvt.h:202:32: runtime error: member call on address 0x7ffd66f63cf0 which does not point to an object of type         '__codecvt_abstract_base'  
 0x7ffd66f63cf0: note: object is of type 'boost::archive::detail::utf8_codecvt_facet'  
  f2 7f 00 00  48 25 8d 4f f2 7f 00 00  00 00 00 00 00 00 00 00  60 1b b8 4d f2 7f 00 00  00 00 00 00  
               ^~~~~~~~~~~~~~~~~~~~~~~  
               vptr for 'boost::archive::detail::utf8_codecvt_facet'  
 a.count=2  
 b.count=1  
 No errors detected.  
```

---

## Comment 1

> Username: robertramey  
> Created at: 2017-11-08 17:17:59 UTC  
> Updated at: 2017-11-08 17:18:49 UTC  
> Url: https://github.com/boostorg/serialization/issues/75#issuecomment-342889096  

It has been my custom to not initialize variables when it was not necessary to do so.  So this is not "undefined behavior".  Hence -fsantize=undefined is not appropriate for this library nor it's tests.
