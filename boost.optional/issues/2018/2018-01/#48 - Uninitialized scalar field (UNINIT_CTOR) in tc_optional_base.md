# #48 - Uninitialized scalar field (UNINIT_CTOR) in tc_optional_base [Closed]

> Username: jeking3  
> Created at: 2018-01-23 14:17:19 UTC  
> Updated at: 2021-04-12 09:52:10 UTC  
> Closed at: 2018-01-24 00:30:25 UTC  
> Url: https://github.com/boostorg/optional/issues/48  

This showed up on a Coverity Scan run in Boost.DateTime:  
  
```  
boost/optional/detail/optional_trivially_copyable_base.hpp:   
  
 protected:  
 15    typedef T &       reference_type ;  
 16    typedef T const&  reference_const_type ;  
 17#ifndef  BOOST_OPTIONAL_DETAIL_NO_RVALUE_REFERENCES  
 18    typedef T &&  rval_reference_type ;  
 19    typedef T &&  reference_type_of_temporary_wrapper ;  
 20#endif  
 21    typedef T *         pointer_type ;  
 22    typedef T const*    pointer_const_type ;  
 23    typedef T const&    argument_type ;  
 24  
 25    tc_optional_base()  
 26      :  
   	  
CID 202344 (#1-3 of 3): Uninitialized scalar field (UNINIT_CTOR)  
2. uninit_member: Non-static class member m_storage is not initialized in this constructor nor in any functions that it calls.  
 27      m_initialized(false) {}  
 28  
 29    tc_optional_base ( none_t )  
 30      :  
 31      m_initialized(false) {}  
 32  
 33    tc_optional_base ( argument_type val )  
```  
  
Coverity Scan issue id:   
  
https://scan4.coverity.com/reports.htm#v17121/p13946/fileInstanceId=35326295&defectInstanceId=6591221&mergedDefectId=202344

---

## Comment 1

> Username: akrzemi1  
> Created at: 2018-01-23 17:48:50 UTC  
> Url: https://github.com/boostorg/optional/issues/48#issuecomment-359873206  

This is on purpose. And I think it is ok to have uninitialized member provided that you do not read its value and  the only thing you do subsequently is to assign a different value to it.

---

## Comment 2

> Username: jeking3  
> Created at: 2018-01-24 00:30:25 UTC  
> Url: https://github.com/boostorg/optional/issues/48#issuecomment-359979472  

I will resolve this as intentional for performance.  Thanks!

---

## Comment 3

> Username: mglisse  
> Created at: 2021-04-11 21:29:37 UTC  
> Url: https://github.com/boostorg/optional/issues/48#issuecomment-817375621  

> And I think it is ok to have uninitialized member provided that you do not read its value and the only thing you do subsequently is to assign a different value to it.  
  
Note that the uninitialized value can be read (copied) by a copy/move of the optional. But this base class is selected only for scalar types, and I expect this condition was chosen precisely so it would be safe to copy an uninitialized value (?). And copying is the only use that is made of the uninitialized value (and of its copies), so your argument should still hold.

---

## Comment 4

> Username: akrzemi1  
> Created at: 2021-04-12 09:52:10 UTC  
> Url: https://github.com/boostorg/optional/issues/48#issuecomment-817666231  

Technically, memcpy-ing an uninitialized scalar is UB.
