# #51 - exclusive_less_than may not be compliant with C++ standard requirements for comparator [Open]

> Username: honnesh  
> Created at: 2025-10-29 20:21:23 UTC  
> Updated at: 2026-02-14 20:21:47 UTC  
> Url: https://github.com/boostorg/icl/issues/51  

Hello,  
  
We recently updated to clang 22 and we found a potential issue with exclusive_less_than implemented in https://github.com/boostorg/icl/blob/develop/include/boost/icl/detail/exclusive_less_than.hpp. As per the C++ standard requirements for a comparator detailed in https://en.cppreference.com/w/cpp/named_req/Compare.html, exclusive_less_than violates the transitive property of equivalence highlighted here:  
  
<img width="482" height="47" alt="Image" src="https://github.com/user-attachments/assets/0d009700-4732-4b27-aeec-fceb2fc67816" />  
  
An example situation is the case of   
`a = [3 5), b = [4 7) and c = [6 8)`  
  
I believe this non-compliance is exposed with clang 22. Please let me know if I am missing something here.

---

## Comment 1

> Username: jofaber  
> Created at: 2026-01-02 11:13:10 UTC  
> Updated at: 2026-01-02 17:59:36 UTC  
> Url: https://github.com/boostorg/icl/issues/51#issuecomment-3705110903  

Dear honnesh,  
Good catch :)  
Your remarks are correct.   
  
To summarize:  
  
- The induced **_incomparability relation_** of comparator `exclusive_less_than` is **_not transitive_**.  
- Therefore the induced **_incomparability relation_** is not an equivalence-relation  
- Therefore `exclusive_less_than` is **not a strict weak order** on intervals  
  
This means that icl's use of std:: containers are not only non-compliant with clang 22. They are non-compliant with the standard template libraries in general.  
  
While this is kind of shocking to realize, fortunately AFAIK this does not affect the correctness of the library. More specifically I do not know of any failing test case, that is related to the above violation of comparator requirements.  
  
The cause of this "functional resilience" is probably the tight maintainance of all containers having non-overlapping intervals.  
  
After digging deeper into the issue I realized that this flaw has to be fixed. So I will work on the necessary changes. I am pretty optimistic this refactoring can be done without any breaking changes to the library.

---

## Comment 2

> Username: honnesh  
> Created at: 2026-01-05 19:58:18 UTC  
> Updated at: 2026-01-05 19:59:01 UTC  
> Url: https://github.com/boostorg/icl/issues/51#issuecomment-3711895246  

Here is a test case that demonstrates the difference in behavior after updating to Clang 22  
  
```  
static void test_upper_bound()  
{  
    boost::icl::interval_set<int> mp;  
    mp.add(boost::icl::right_open_interval<int>(0, 2));  
    mp.add(boost::icl::right_open_interval<int>(3, 5));  
    mp.add(boost::icl::right_open_interval<int>(6, 9));  
    mp.add(boost::icl::right_open_interval<int>(10, 12));  
  
    auto it = mp.upper_bound(boost::icl::right_open_interval<int>(4, 7));  
    assert(it != end(mp));  
  
    // Expected: [10 12)  
    std::cout << it->lower() << ' '<< it->upper() << std::endl;  
}  
```  
  
Please add this to your internal test suite if don't have an equivalent test already.

---

## Comment 3

> Username: honnesh  
> Created at: 2026-01-06 16:33:40 UTC  
> Url: https://github.com/boostorg/icl/issues/51#issuecomment-3715393250  

Here is an equivalent test that doesn't involve interval_set but mimics its functionality through the comparator used by interval_set  
  
```  
struct exclusive_less_than  
{  
    bool operator()(const std::pair<int, int>& lhs, const std::pair<int, int>& rhs) const  
    {  
        return lhs.second < rhs.first;  
    }  
};  
  
static void test_upper_bound()  
{  
    std::set<std::pair<int, int>, exclusive_less_than> mp;  
    mp.insert(std::make_pair(0, 2));  
    mp.insert(std::make_pair(3, 5));  
    mp.insert(std::make_pair(6, 9));  
    mp.insert(std::make_pair(10, 12));  
  
    auto it = mp.upper_bound(std::make_pair(4, 7));  
    assert(it != end(mp));  
  
    // Expected: 10 12  
    std::cout << it->first << ' '<< it->second << std::endl;  
}  
```

---

## Comment 4

> Username: jofaber  
> Created at: 2026-01-16 14:47:34 UTC  
> Url: https://github.com/boostorg/icl/issues/51#issuecomment-3760353146  

Thank you for the report and the test cases. I am working on it.

---

## Comment 5

> Username: jofaber  
> Created at: 2026-02-14 20:21:47 UTC  
> Url: https://github.com/boostorg/icl/issues/51#issuecomment-3902457060  

I’ve investigated the reported behavior using a self-built clang-22 (from the llvm-project repository) together with the corresponding libc++ from the same source tree.  
  
I was not able to reproduce any behavioral change compared to earlier toolchains.  
  
In particular:  
- I exercised `lower_bound` and `upper_bound` with the data provided in the issue (see [test case](https://github.com/boostorg/icl/blob/9fd8c784f596104708a93327fafe2750cd398fe3/test/fix_tickets_/fix_issues.cpp).  
- I ran the complete `Boost.ICL` test suite under clang-22.  
- All tests pass and all observed results match the expected behavior.  
  
While it is true that the comparator `exclusive_less_than` does not induce a transitive equivalence relation (and therefore is not a strict weak ordering in the abstract sense), I could not find any evidence that this non-compliance is newly “exposed” by clang-22 or newer libc++ releases.  
  
With the current test coverage and experiments, `Boost.ICL` does not appear to be broken, nor does the behavior of the standard library algorithms seem to have changed in a way that affects `ICL`.  
  
This is a preliminary result; if a minimal reproducer exists that demonstrates different behavior under clang-22 compared to earlier versions, I’d be happy to investigate further.
