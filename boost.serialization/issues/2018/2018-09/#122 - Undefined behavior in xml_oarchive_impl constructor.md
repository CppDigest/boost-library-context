# #122 - Undefined behavior in xml_oarchive_impl constructor [Open]

> Username: pdimov  
> Created at: 2018-09-30 15:22:08 UTC  
> Updated at: 2018-11-07 21:39:54 UTC  
> Url: https://github.com/boostorg/serialization/issues/122  

```  
====== BEGIN OUTPUT ======  
boost/archive/detail/interface_oarchive.hpp:47:16: runtime error: downcast of address 0x7ffdfddb9c00 which does not point to an object of type 'boost::archive::xml_oarchive'  
0x7ffdfddb9c00: note: object is of type 'boost::archive::xml_oarchive_impl<boost::archive::xml_oarchive>'  
 26 7f 00 00  18 e5 62 4a 26 7f 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::archive::xml_oarchive_impl<boost::archive::xml_oarchive>'  
    #0 0x7f264a1b33ba in boost::archive::detail::interface_oarchive<boost::archive::xml_oarchive>::This() /home/travis/build/boostorg/boost-root/./boost/archive/detail/interface_oarchive.hpp:47:16  
    #1 0x7f264a1b3452 in boost::archive::basic_xml_oarchive<boost::archive::xml_oarchive>::init() /home/travis/build/boostorg/boost-root/./boost/archive/impl/basic_xml_oarchive.ipp:240:11  
    #2 0x7f264a1b71fe in boost::archive::xml_oarchive_impl<boost::archive::xml_oarchive>::xml_oarchive_impl(std::ostream&, unsigned int) /home/travis/build/boostorg/boost-root/./boost/archive/impl/xml_oarchive_impl.ipp:114:15  
    #3 0x429e49 in boost::archive::xml_oarchive::xml_oarchive(std::ostream&, unsigned int) /home/travis/build/boostorg/boost-root/./boost/archive/xml_oarchive.hpp:122:9  
```  
  
In the constructor of `xml_oarchive_impl<Archive>`, the object is still of type `xml_oarchive_impl`, the derived class `Archive` does not exist yet.  
  
It then calls `init`, which uses `This()`, which static_casts `this` to `Archive*`, which is an error.

---

## Comment 1

> Username: robertramey  
> Created at: 2018-10-01 00:49:59 UTC  
> Url: https://github.com/boostorg/serialization/issues/122#issuecomment-425765117  

This is a long standing issue.  I haven't been able to fix it without changing the library interface.   Actually it was only years after the library has been in use that the issue was discovered.  It seems that, though undefined, the actual behavior is that which one would expect.  It hasn't caused  a problem so I just left it.  
  
I'll leave this issue open indefinitely as a reminder.

---

## Comment 2

> Username: jeking3  
> Created at: 2018-11-07 21:39:53 UTC  
> Url: https://github.com/boostorg/serialization/issues/122#issuecomment-436787000  

This looks like a dupe of #108?
