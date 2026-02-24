# #64 Fix splice for slist [Closed]

> Username: QUvalda  
> Created at: 2017-12-18 13:21:54 UTC  
> Updated at: 2017-12-21 11:14:14 UTC  
> Closed at: 2017-12-21 11:14:14 UTC  
> Url: https://github.com/boostorg/container/pull/64  

The call of the splice method with iterators leads to an infinite loop inside common_slist_algorithms::get_previous_node  
  
slist<int> lst1 = { 0, 1, 2, 3 };  
slist<int> lst2;  
lst2.splice(lst2.begin(), lst1, lst1.begin());  
  
expected:  
lst1 == { 1, 2, 3 }  
lst2 == { 0 }

---

## Comment 1

> Username: QUvalda  
> Created_at: 2017-12-18 15:05:30 UTC  
> Url: https://github.com/boostorg/container/pull/64#issuecomment-352451164  

xref https://svn.boost.org/trac10/ticket/13348

---

## Review 2 [Commented]

> Username: igaztanaga  
> Created_at: 2017-12-20 11:47:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/container/pull/64#pullrequestreview-84738552  

📁 test/global_resource_test.cpp

```diff
  33 | 
  42 |- void* operator new[](std::size_t count) BOOST_CONTAINER_NEW_EXCEPTION_SPECIFIER
  34 |+ void* operator new[](std::size_t count) BOOST_NOEXCEPT_IF(false)
```

> Username: igaztanaga  
> Created_at: 2017-12-20 11:47:26 UTC  
> Url: https://github.com/boostorg/container/pull/64#discussion_r158002615  

We need to support older containers that don't have noexcept and avoid warnings that tell us that new should throw std::bad_alloc. What is the problem with current code?

> Username: QUvalda  
> Created_at: 2017-12-20 20:44:50 UTC  
> Url: https://github.com/boostorg/container/pull/64#discussion_r158131141  

Hm. I do not really understand why this commit is displayed here. To fix the splice it has nothing to do.  I did this local fix in my repository only to fix the build. But if you look at the Travis CI build (8.2), you can see next error:  
  
libs/container/test/global_resource_test.cpp:42:41: error: ISO C++17 does not allow dynamic exception specifications [-Wdynamic-exception-spec]  
  
void* operator new[](std::size_t count) BOOST_CONTAINER_NEW_EXCEPTION_SPECIFIER  
  
libs/container/test/global_resource_test.cpp:34:52: note: expanded from macro 'BOOST_CONTAINER_NEW_EXCEPTION_SPECIFIER'  
  
#define BOOST_CONTAINER_NEW_EXCEPTION_SPECIFIER    throw(std::bad_alloc)  
  
libs/container/test/global_resource_test.cpp:42:41: note: use 'noexcept(false)' instead

> Username: igaztanaga  
> Created_at: 2017-12-21 11:12:55 UTC  
> Url: https://github.com/boostorg/container/pull/64#discussion_r158252049  

Ok, can you please open a new issue for this error, adding as much context as you can (compiler, version, flags, etc.) . I'll close this issue as your main commit was already merged. Many thanks for the report and the patch.


---

## Comment 3

> Username: igaztanaga  
> Created_at: 2017-12-21 11:14:14 UTC  
> Url: https://github.com/boostorg/container/pull/64#issuecomment-353325543  

Fixed cherry-picking commit:  
  
https://github.com/boostorg/container/commit/ed6c8bd87d95eb8be54e133c901a50b5bcbbc288  
  
Many thanks for the report.

---
