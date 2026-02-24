# #36 - Erroneous usage of `BOOST_TEST` in `test::detail::memory_tracker::~memory_tracker` [Closed]

> Username: cmazakas  
> Created at: 2021-11-18 16:48:13 UTC  
> Updated at: 2021-11-19 02:26:33 UTC  
> Closed at: 2021-11-19 02:26:33 UTC  
> Url: https://github.com/boostorg/unordered/issues/36  

The `memory_tracker` is a helper used by the allocators in the test suite to make sure that allocate + construct calls are matched up properly with destruct + deallocation calls. The `memory_tracker` also keeps track of how many allocators are alive and referencing it.  
  
Unfortunately, the destructor's usage of `BOOST_TEST` does not work as was intended by the original author.  
  
The destructor is defined [here](https://github.com/boostorg/unordered/blob/1e553df5b6603c57ee98cb239eb29ea205466ec3/test/helpers/memory.hpp#L71):  
```cpp  
~memory_tracker() { BOOST_TEST(count_allocators == 0); }  
```  
  
and the memory tracker is included in the unit tests as an [object in namespace scope](https://github.com/boostorg/unordered/blob/1e553df5b6603c57ee98cb239eb29ea205466ec3/test/helpers/memory.hpp#L154-L163):  
```cpp  
namespace detail {  
  // This won't be a problem as I'm only using a single compile unit  
  // in each test (this is actually required by the minimal test  
  // framework).  
  //  
  // boostinspect:nounnamed  
  namespace {  
    test::detail::memory_tracker tracker;  
  }  
}  
```  
  
Unfortunately, this destructor does _not_ have the desired effect when running the test suite. Namely, tests in Unordered are run via [this macro](https://github.com/boostorg/unordered/blob/1e553df5b6603c57ee98cb239eb29ea205466ec3/test/helpers/test.hpp#L26-L32):  
```cpp  
#define RUN_TESTS()                                                            \  
  int main(int, char**)                                                        \  
  {                                                                            \  
    BOOST_UNORDERED_TEST_COMPILER_INFO()                                       \  
    ::test::get_state().run_tests();                                           \  
    return boost::report_errors();                                             \  
  }  
```  
  
The `tracker`'s lifetime is static which means that its destructor is run after main has already ended so all errors are ignored by the test runner. To this end, a developer can erroneously refactor the `cxx11_allocator` and mess up the reference counting without even knowing.

---

## Comment 1

> Username: cmazakas  
> Created at: 2021-11-18 17:04:04 UTC  
> Url: https://github.com/boostorg/unordered/issues/36#issuecomment-973062299  

@pdimov @glenfe I wanted to pick your guys' brain on how to best refactor this code.  
  
My first instinct was to refactor the code to rework the lifetimes so that each allocator held a shared pointer to the tracker. But this is problematic because I can't think of a way that wouldn't be API breaking in the test suite. And that's problematic because it's a deeper refactor of the test suite and would be a huge change.  
  
My other instinct was to be lazy and just turn the `BOOST_TEST` call into a `BOOST_ASSERT` call. To this end, it turns an ignored error into a hard failure caught by the test runner and requires no other refactoring of the codebase.

---

## Comment 2

> Username: pdimov  
> Created at: 2021-11-18 17:16:59 UTC  
> Url: https://github.com/boostorg/unordered/issues/36#issuecomment-973078958  

Using `BOOST_ASSERT` should be fine.
