# #161 include/boost/serialization/utility.hpp: Allow non-default constructible members in std::pair [Closed]

> Username: mgehre  
> Created at: 2019-06-06 10:15:50 UTC  
> Updated at: 2020-07-18 17:29:26 UTC  
> Closed at: 2020-07-18 17:29:26 UTC  
> Url: https://github.com/boostorg/serialization/pull/161  

Allow to serialize `std::pair` (and `std:map` etc) when they contain a non-default constructible type.  
  
I moved `ar & boost::serialization::make_nvp("first", p->first);` out of `serialize` into `save_construct_data`/`load_construct_data` to avoid an undefined behavior sanitizer diagnostic   
`runtime error: load of value 4294956849, which is not a valid value for type 'my_enum'"`  
on line `::new (p)::std::pair<F, S>(boost::move(first.reference()), boost::move(second.reference()));`  
when F or S is a bool or enum (`stack_construct` then leaves them uninitialized).  
  
One test fails with this PR, namely  
`libs/serialization/test/test_variant.cpp(270): test '(h_ptr) == (h_ptr2)' failed in function 'void test_variant_map()'`.  
This test case reads  
```  
    map_t map;  
    {  
        test_ostream os(testfile, TEST_STREAM_FLAGS);  
        test_oarchive oa(os, TEST_ARCHIVE_FLAGS);  
        H const h = {5};  
        variant_t v(h);  
        map[0] = v;  
        BOOST_ASSERT(1 == map.size());  
        oa << boost::serialization::make_nvp("written", map);  
        H const * const h_ptr = boost::strict_get<H const>(&map[0]);  
        BOOST_CHECK_EQUAL(h_ptr, boost::strict_get<H const>(&map[0]));  
        oa << boost::serialization::make_nvp("written", h_ptr);  
    }  
    map_t map2;  
    {  
        test_istream is(testfile, TEST_STREAM_FLAGS);  
        test_iarchive ia(is, TEST_ARCHIVE_FLAGS);  
        ia >> boost::serialization::make_nvp("written", map2);  
        BOOST_ASSERT(1 == map2.size());  
        H * h_ptr;  
        ia >> boost::serialization::make_nvp("written", h_ptr);  
        H const * const h_ptr2 = boost::strict_get<H const>(&map2[0]);  
        BOOST_CHECK_EQUAL(h_ptr, h_ptr2);  
    }  
```  
Any hints how I could fix that pointer tracking issue?

---

## Comment 1

> Username: mgehre  
> Created_at: 2019-12-19 14:20:34 UTC  
> Url: https://github.com/boostorg/serialization/pull/161#issuecomment-567508429  

Rebased to fix conflicts.

---

## Comment 2

> Username: robertramey  
> Created_at: 2020-07-18 17:29:26 UTC  
> Url: https://github.com/boostorg/serialization/pull/161#issuecomment-660514933  

I've got more than a few problems with this.  
  
a) it fails all tests  
b) it changes the semantics - ie file storage - without maintaining compatibility with older files.  This is likely impossible to do anyway since std::pair is unversioned.  
  
I understand the inconvenience of dealing with types which have no default ctor, but we'll have to find another way to deal with this.

---
