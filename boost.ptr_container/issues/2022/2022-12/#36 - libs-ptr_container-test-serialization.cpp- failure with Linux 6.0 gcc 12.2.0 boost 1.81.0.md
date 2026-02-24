# #36 - libs/ptr_container/test/serialization.cpp: failure with Linux 6.0 gcc 12.2.0 boost 1.81.0 [Closed]

> Username: sgn  
> Created at: 2022-12-17 05:34:26 UTC  
> Updated at: 2023-02-23 15:31:07 UTC  
> Closed at: 2023-02-23 15:31:07 UTC  
> Url: https://github.com/boostorg/ptr_container/issues/36  

```  
testing.capture-output bin.v2/libs/ptr_container/test/serialization.test/gcc-12/release/threading-multi/visibility-hidden/serialization.run  
====== BEGIN OUTPUT ======                                                                                                                                                                      
Running 1 test case...                                                                                                                                                                          
libs/ptr_container/test/serialization.cpp(206): error: in "Pointer Container Test Suite/test_serialization": check (*vec2.begin()).i == -1 has failed [0 != -1]  
libs/ptr_container/test/serialization.cpp(207): error: in "Pointer Container Test Suite/test_serialization": check (*++vec2.begin()).i == 0 has failed [-1 != 0]  
libs/ptr_container/test/serialization.cpp(206): error: in "Pointer Container Test Suite/test_serialization": check (*vec2.begin()).i == -1 has failed [0 != -1]  
libs/ptr_container/test/serialization.cpp(207): error: in "Pointer Container Test Suite/test_serialization": check (*++vec2.begin()).i == 0 has failed [-1 != 0]  
                                                                                                                                                                                                
*** 4 failures are detected in the test module "Master Test Suite"    
```

---

## Comment 1

> Username: dkrejsa  
> Created at: 2023-02-11 19:00:21 UTC  
> Url: https://github.com/boostorg/ptr_container/issues/36#issuecomment-1426854414  

I see the same thing with Boost 1.81 using Clang 15.0.0 in the VxWorks port.  
Is there any reason to expect that for the unordered containers case, the serialized-then-deserialized version of the collection would have its elements in the same order as the original collection?

---

## Comment 2

> Username: dkrejsa  
> Created at: 2023-02-11 19:19:59 UTC  
> Updated at: 2023-02-11 19:25:41 UTC  
> Url: https://github.com/boostorg/ptr_container/issues/36#issuecomment-1426858089  

Possible alternative test_serialization_unordered_set_helper() implementation:  
```  
template< class Cont, class OArchive, class IArchive >  
void test_serialization_unordered_set_helper()  
{  
    Cont vec;  
    set_capacity<Cont>()( vec );  
    add( vec, new Base( -1 ), 0u );  
    add( vec, new Derived( 1 ), 1u );  
    BOOST_CHECK_EQUAL( vec.size(), 2u );  
  
    std::string fn = std::tmpnam( 0 );  
  
    {  
        std::ofstream ofs( fn.c_str() );  
        OArchive oa(ofs);  
        oa << boost::serialization::make_nvp( "container", as_const(vec) );  
    }  
  
    Cont vec2;  
  
    {  
        std::ifstream ifs( fn.c_str(), std::ios::binary );  
        IArchive ia(ifs);  
        ia >> boost::serialization::make_nvp( "container", vec2 );  
    }  
  
    std::remove( fn.c_str() );  
  
    BOOST_CHECK_EQUAL( vec.size(), vec2.size() );  
  
  
    // Note, as unordered containers, vec2 may have its elements in a different  
    // order than vec.  
  
    typename Cont::iterator it = vec2.begin();  
  
    int iFirst = (*it).i;  
    int iSecond = (*++vec2.begin()).i;  
  
    if (iFirst == -1) {  
        BOOST_CHECK_EQUAL (iSecond, 0);  
        ++it;  
    } else {  
        BOOST_CHECK_EQUAL (iFirst, 0);  
        BOOST_CHECK_EQUAL (iSecond, -1);  
    }  
  
    Derived* d = dynamic_cast<Derived*>( &*it );  
    BOOST_CHECK_EQUAL( d->i2, 1 );  
}  
```
