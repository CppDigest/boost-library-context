# #237 - Clang-cl's -Wdelete-non-abstract-non-virtual-dtor triggered by test_case_gen [Closed]

> Username: rubenvb  
> Created at: 2019-11-25 09:02:38 UTC  
> Updated at: 2020-01-07 06:43:47 UTC  
> Closed at: 2020-01-07 06:43:17 UTC  
> Url: https://github.com/boostorg/test/issues/237  

```  
In file included from include\boost-1_70\boost/test/unit_test.hpp:18:  
In file included from include\boost-1_70\boost/test/test_tools.hpp:46:  
In file included from include\boost-1_70\boost/test/tools/old/impl.hpp:20:  
In file included from include\boost-1_70\boost/test/tools/assertion_result.hpp:21:  
In file included from include\boost-1_70\boost/shared_ptr.hpp:17:  
In file included from include\boost-1_70\boost/smart_ptr/shared_ptr.hpp:26:  
In file included from include\boost-1_70\boost/checked_delete.hpp:15:  
boost/core/checked_delete.hpp(36,5): error : delete called on non-final 'boost::unit_test::data::ds_detail::test_case_gen<TestSuiteName::testCaseName, boost::unit_test::data::monomorphic::generated_by<boost::unit_test::data::monomorphic::xrange_t<int, int> > >' that has virtual functions but non-virtual destructor  
include\boost-1_70\boost/test/data/test_case.hpp(326,22): message : expanded from macro 'BOOST_DATA_TEST_CASE'  
include\boost-1_70\boost/test/data/test_case.hpp(326,22): message : In file included from Test.cpp:8:  
include\boost-1_70\boost/test/data/test_case.hpp(326,22): message : In file included from include\boost-1_70\boost/test/unit_test.hpp:18:  
include\boost-1_70\boost/test/data/test_case.hpp(326,22): message : In file included from include\boost-1_70\boost/test/test_tools.hpp:46:  
include\boost-1_70\boost/test/data/test_case.hpp(326,22): message : In file included from include\boost-1_70\boost/test/tools/old/impl.hpp:20:  
nclude\boost-1_70\boost/test/data/test_case.hpp(326,22): message : In file included from include\boost-1_70\boost/test/tools/assertion_result.hpp:21:  
include\boost-1_70\boost/test/data/test_case.hpp(326,22): message : In file included from include\boost-1_70\boost/shared_ptr.hpp:17:  
include\boost-1_70\boost/test/data/test_case.hpp(326,22): message : In file included from include\boost-1_70\boost/smart_ptr/shared_ptr.hpp:26:  
include\boost-1_70\boost/test/data/test_case.hpp(326,22): message : In file included from include\boost-1_70\boost/checked_delete.hpp:15:  
```  
  
This should be triggered by   
```  
#include <boost/test/unit_test.hpp>  
#include <boost/test/data/test_case.hpp>  
BOOST_AUTO_TEST_SUITE(TestSuiteName)  
  
BOOST_DATA_TEST_CASE(testCaseName, boost::unit_test::data::xrange(1, 4), test_case_number)  
{}  
  
BOOST_AUTO_TEST_SUITE_END()  
```  
  
Compiled with  
```  
clang-cl /W4 /I"Path/To/Boost/" Test.cpp  
```

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-11-25 12:36:37 UTC  
> Url: https://github.com/boostorg/test/issues/237#issuecomment-558136664  

Thanks for the report. Would it be possible for you to preprocess that file and to indicate me the definition of `test_unit_generator` on that preprocessed file? This is the base class and it has `virtual` dtor, but it seems that this is under some compiler assumptions.

---

## Comment 2

> Username: rubenvb  
> Created at: 2019-11-25 13:46:34 UTC  
> Url: https://github.com/boostorg/test/issues/237#issuecomment-558162586  

The preprocessed file contains this:  
```  
class test_unit_generator {  
public:  
    virtual test_unit* next() const = 0;  
  
protected:  
                                 ~test_unit_generator() {}  
};  
```  
So no `virtual`. Seems a strange somewhat important detail to `#ifdef` to me though...  
I can provide the full preprocessed file if necessary, but I'd have to make sure no work details leak into it first though, or generate one when I get home.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2019-11-25 20:03:29 UTC  
> Url: https://github.com/boostorg/test/issues/237#issuecomment-558318474  

There is a macro `BOOST_TEST_PROTECTED_VIRTUAL` that is defined like this:  
  
```  
#if defined(__GNUC__) || BOOST_WORKAROUND(BOOST_MSVC, == 1400)  
#define BOOST_TEST_PROTECTED_VIRTUAL virtual  
#else  
#define BOOST_TEST_PROTECTED_VIRTUAL  
#endif  
```  
  
and that is in front of the definition of `~test_unit_generator()`. I guess for clang, it does not get expanded properly, or simply the condition dating back to 2005 is plain wrong. If you change the condition to   
  
```  
#if defined(__GNUC__) || defined(BOOST_CLANG) || BOOST_WORKAROUND(BOOST_MSVC, == 1400)  
```  
  
I believe it should work.

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2019-12-12 20:47:40 UTC  
> Url: https://github.com/boostorg/test/issues/237#issuecomment-565180008  

Possible fix in `topic/GH-237-non-abstract-non-virtual-dtor`. It would be nice if you can give a try to that branch.

---

## Comment 5

> Username: rubenvb  
> Created at: 2019-12-13 18:36:56 UTC  
> Url: https://github.com/boostorg/test/issues/237#issuecomment-565557489  

It seems to do the trick. Thanks!

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2020-01-07 06:43:17 UTC  
> Url: https://github.com/boostorg/test/issues/237#issuecomment-571458349  

Merged to master
