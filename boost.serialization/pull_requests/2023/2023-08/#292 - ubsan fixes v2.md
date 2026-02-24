# #292 ubsan fixes v2 [Merged]

> Username: cmazakas  
> Created at: 2023-08-25 15:08:48 UTC  
> Updated at: 2023-10-20 20:42:38 UTC  
> Merged at: 2023-10-20 20:42:38 UTC  
> Closed at: 2023-10-20 20:42:38 UTC  
> Url: https://github.com/boostorg/serialization/pull/292  

I felt bad about breaking Serialization with a reinterpret_cast so I re-investigated the ubsan failures I had encountered.  
  
It turns out, both are legitimate!  
  
One was caused because we were accessing `xml_oarchive` from the `xml_oarchive_impl` destructor and the other was because we were accessing a member function from a member initializer list which gcc doesn't like  
  
I've created two minimum reproducers on Complier Explorer.  
  
https://godbolt.org/z/YGGa5z7bM  
https://godbolt.org/z/ahWT78KsY  
  
I'm also going to ping @pdimov on this PR as well.  
  
Hopefully, this should remove the need for manually suppressing santizers and also fix the UB and not crash msvc.  
  
Here's the sanitizer output on my machine, for reference:  
```txt  
testing.capture-output ../../bin.v2/libs/serialization/test/test_mult_archive_types.test/clang-linux-16/debug/cxxstd-20-iso/link-static/sanitize-undefined/threading-multi/visibility-hidden/test_mult_archive_types.run  
====== BEGIN OUTPUT ======  
../../boost/archive/detail/interface_oarchive.hpp:48:16: runtime error: downcast of address 0x7ffd06ad9d00 which does not point to an object of type 'boost::archive::xml_oarchive'  
0x7ffd06ad9d00: note: object is of type 'boost::archive::xml_oarchive_impl<boost::archive::xml_oarchive>'  
 0b 7f 00 00  58 d2 90 40 c5 55 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::archive::xml_oarchive_impl<boost::archive::xml_oarchive>'  
    #0 0x55c54066d342 in boost::archive::detail::interface_oarchive<boost::archive::xml_oarchive>::This() /home/exbigboss/cpp/boost-root/libs/serialization/../../boost/archive/detail/interface_oarchive.hpp:48:16  
    #1 0x55c5406f02aa in boost::archive::basic_xml_oarchive<boost::archive::xml_oarchive>::windup() /home/exbigboss/cpp/boost-root/libs/serialization/../../boost/archive/impl/basic_xml_oarchive.ipp:253:11  
    #2 0x55c5406f31e9 in boost::archive::xml_oarchive_impl<boost::archive::xml_oarchive>::~xml_oarchive_impl() /home/exbigboss/cpp/boost-root/libs/serialization/../../boost/archive/impl/xml_oarchive_impl.ipp:135:15  
    #3 0x55c5406902e8 in boost::archive::xml_oarchive::~xml_oarchive() /home/exbigboss/cpp/boost-root/libs/serialization/../../boost/archive/xml_oarchive.hpp:127:37  
    #4 0x55c54065b64c in void test_save_and_load<boost::archive::xml_oarchive, boost::archive::xml_iarchive>(A*, A*) /home/exbigboss/cpp/boost-root/libs/serialization/test/test_mult_archive_types.cpp:82:5  
    #5 0x55c5406597c8 in test_main(int, char**) /home/exbigboss/cpp/boost-root/libs/serialization/test/test_mult_archive_types.cpp:115:5  
    #6 0x55c54065944c in main /home/exbigboss/cpp/boost-root/libs/serialization/test/test_tools.hpp:202:18  
    #7 0x7f0bc5229d8f in __libc_start_call_main csu/../sysdeps/nptl/libc_start_call_main.h:58:16  
    #8 0x7f0bc5229e3f in __libc_start_main csu/../csu/libc-start.c:392:3  
    #9 0x55c540630854 in _start (/home/exbigboss/cpp/boost-root/bin.v2/libs/serialization/test/test_mult_archive_types.test/clang-linux-16/debug/cxxstd-20-iso/link-static/sanitize-undefined/threading-multi/visibility-hidden/test_mult_archive_types+0xef854) (BuildId: 1a4b736365b206f3be6783a6fe838fe919da0595)  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../boost/archive/detail/interface_oarchive.hpp:48:16 in  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
  
and  
  
```txt  
testing.capture-output ../../bin.v2/libs/serialization/test/test_exported_polymorphic_xml_archive.test/gcc-12/debug/cxxstd-20-iso/link-static/sanitize-undefined/threading-multi/visibility-hidden/test_exported_polymorphic_xml_archive.run  
====== BEGIN OUTPUT ======  
../../boost/serialization/extended_type_info_no_rtti.hpp:104:61: runtime error: member call on address 0x55ca2344d7c0 which does not point to an object of type 'extended_type_info_no_rtti'  
0x55ca2344d7c0: note: object has invalid vptr  
 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 01 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              invalid vptr  
    #0 0x55ca2320f6bf in boost::serialization::extended_type_info_no_rtti<polymorphic_derived1>::extended_type_info_no_rtti() ../../boost/serialization/extended_type_info_no_rtti.hpp:104  
    #1 0x55ca2320e1d6 in boost::serialization::detail::singleton_wrapper<boost::serialization::extended_type_info_no_rtti<polymorphic_derived1> >::singleton_wrapper() ../../boost/serialization/singleton.hpp:147  
    #2 0x55ca2320d7c7 in boost::serialization::singleton<boost::serialization::extended_type_info_no_rtti<polymorphic_derived1> >::get_instance() ../../boost/serialization/singleton.hpp:171  
    #3 0x55ca23202fe5 in __static_initialization_and_destruction_0 ../../boost/serialization/singleton.hpp:207  
    #4 0x55ca23203108 in _GLOBAL__sub_I__ZN5boost7archive6tmpnamEPc test/test_exported.cpp:102  
    #5 0x7f9313029eba in call_init ../csu/libc-start.c:145  
    #6 0x7f9313029eba in __libc_start_main_impl ../csu/libc-start.c:379  
    #7 0x55ca232011d4 in _start (/home/exbigboss/cpp/boost-root/bin.v2/libs/serialization/test/test_exported_polymorphic_xml_archive.test/gcc-12/debug/cxxstd-20-iso/link-static/sanitize-undefined/threading-multi/visibility-hidden/test_exported_polymorphic_xml_archive+0x1151d4)  
  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
```

---

## Comment 1

> Username: mborland  
> Created_at: 2023-08-25 17:42:30 UTC  
> Url: https://github.com/boostorg/serialization/pull/292#issuecomment-1693722432  

Turns out manually suppressing them introduced this bug: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=78204.

---
