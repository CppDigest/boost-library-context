# #253 Add tests for iterator_range<const pointer> and pair<const pointer> [Merged]

> Username: sdebionne  
> Created at: 2015-03-09 09:21:22 UTC  
> Updated at: 2015-03-09 12:35:00 UTC  
> Merged at: 2015-03-09 12:01:18 UTC  
> Closed at: 2015-03-09 12:01:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/253  

This PR adds tests for `iterator_range<const pointer>` and `pair<const pointer>`.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-03-09 09:57:06 UTC  
> Updated_at: 2015-03-09 10:29:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/253#discussion_r26023856  

My proposal would be to merge the tests for pointers and const pointers as follows:  
  
```  
template <typename Iterator>  
void test_pointers()  
{  
    typename std::iterator_traits<Iterator>::value_type arr[10]  
        = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};  
    boost::iterator_range<Iterator> r1(arr, arr + 10);  
    std::pair<Iterator, Iterator> r2(arr, arr + 10);  
  
    BOOST_CHECK(bgr::front(r1) == 0);  
    BOOST_CHECK(bgr::front(r2) == 0);  
    BOOST_CHECK(bgr::back(r1) == 9);  
    BOOST_CHECK(bgr::back(r2) == 9);  
    BOOST_CHECK(bgr::at(r1, 5) == 5);  
    BOOST_CHECK(bgr::at(r2, 5) == 5);  
}  
```  
  
and then call the tests as:  
  
```  
   test_pointers<int*>();  
   test_pointers<int const*>();  
```  
  
Also at the beginning of the file, `<iterator>` must be included.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-03-09 09:59:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/253#issuecomment-77826391  

**IMPORTANT:** This PR requires PR #252 in order to work.

---

## Comment 3

> Username: sdebionne  
> Created_at: 2015-03-09 10:20:49 UTC  
> Updated_at: 2015-03-09 10:29:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/253#discussion_r26024973  

That's indeed a more elegant solution. I'll amend the commit with your implementation.  
  
BTW, I have an other PR in preparation that reworks the tests ([util] tests for now). The big picture is:   
- In Jamfile.v2 use `unit-test` rather than the deprecate `test-suite` rule  
- Use more functionality of Boost.Test (such as automated test suite, templated test cases)   
- Use the prebuild Boost.Test (not the inline one) framework.  
  
The benefits are:  
- A better test report when invoking  `b2 test`  
- The possibility to run a single test suite (e.g. `b2 range`) .  
- Tests are faster to compile

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-03-09 10:30:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/253#discussion_r26025470  

Big picture:  
- I have to admit I am not a Jamfile savvy, so I will leave it to the others to comment on it.  
- Are you talking about `BOOST_AUTO_TEST_CASE` etc.? I already use this in the unit tests I have written from scratch.  
- Please **DO NOT** use the prebuild Boost.Test framework. Boost.Geometry is a header only library, including its tests (and this is hard requirement). Relying on the prebuild Boost.Test framework would violate the header-only requirement.  
  
Benefits:  
- I agree.  
- I thought this was possible already. I am probably missing something.  
- True. We prefer to sacrifice compilation speed for header-only.

---

## Comment 5

> Username: sdebionne  
> Created_at: 2015-03-09 11:01:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/253#discussion_r26026999  

To be honest I inspected the tests in [util] only, so there may be other parts of the lib that use more functionality of Boost.Test. Yes, I'm talking about things like test suites/cases with automated registration (e.g BOOST_AUTO_TEST_SUITE( Boost Geometry [util]).  
  
Does the _tests_ have to be header only ? That sounds like a weird requirement...  
  
Here is what I have in mind for the Jamefile:  
  
```  
project geometry/util/test  
   : source-location .  
   : requirements  
     <source>/boost/test//boost_unit_test_framework  
   ;  
  
unit-test range : range.cpp ;   
```  
  
This does not change the header only requirement for Boost.Geometry, but requires to build boost_unit_test_framework for testing (the previous lines are enough to trigger the compilation and link of the framework).

---

## Comment 6

> Username: mkaravel  
> Created_at: 2015-03-09 11:03:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/253#issuecomment-77834464  

About the pre-build Unit Test Framework (UTF): one solution is to agree on a macro which, when enabled, will allow for testing using the pre-build UTF.  
  
I would suggest that you initiate such a discussion on the BG mailing list.

---

## Comment 7

> Username: sdebionne  
> Created_at: 2015-03-09 11:07:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/253#discussion_r26027284  

The reason I'm suggesting these changes is that, with current system, I overlooked the failing test (the missing const of PR#243) last time...

---

## Comment 8

> Username: mkaravel  
> Created_at: 2015-03-09 11:07:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/253#discussion_r26027297  

We have had this discussion in the past. I do not remember if it was internal (between the main BG developers only or not), and the outcome was to stick with header-only also in tests.  
  
Whether it is weird or not, it is really up to your point-of-view. I can see advantages in both cases. From the library's point-of-view, staying with header-only means one less requirement/dependency.

---

## Comment 9

> Username: mkaravel  
> Created_at: 2015-03-09 11:10:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/253#discussion_r26027459  

I do not see how making the tests depend on the pre-build UTF would have allowed you to spot the problem. I spotted it immediately once I pulled after the merge of your PR, and ran the unit tests.  
  
I am not trying to be negative. I am just trying to understand what is really the advantage of using the pre-build UTF (apart from compilation time).

---

## Comment 10

> Username: sdebionne  
> Created_at: 2015-03-09 11:19:59 UTC  
> Updated_at: 2015-03-09 11:21:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/253#discussion_r26027906  

Pre-build UTF is just about speeding up the tests. It's the use of the `unit-test` rule that would have reported a failing test case at the end of the `b2` execution that could have helped.  
  
'Weird' was probably a bit to strong... What are the advantages of header-only tests (especially when the test framework is bundled and already integrated to the build system) ?

---

## Comment 11

> Username: mkaravel  
> Created_at: 2015-03-09 11:24:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/253#discussion_r26028149  

I was not aware that the `unit-test` rule helps in that. This has to be investigated indeed.  
  
Regarding the advantages: I think you benefited from it in another PR: you downloaded boost, ran `b2 headers` and were ready to go and test....

---

## Comment 12

> Username: sdebionne  
> Created_at: 2015-03-09 12:01:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/253#discussion_r26029904  

With the Jamfile snippet above, the workflow is identical. The only visible difference for the user is the (automatic) build of the UTF framework once, before the compilation of the tests themselves.  
I'll try to publish a branch later today to demonstrate that.

---
