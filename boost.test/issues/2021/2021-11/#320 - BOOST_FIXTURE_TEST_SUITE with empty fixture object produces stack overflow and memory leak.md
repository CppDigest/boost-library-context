# #320 - BOOST_FIXTURE_TEST_SUITE with empty fixture object produces stack overflow and memory leak [Closed]

> Username: vkgl  
> Created at: 2021-11-30 14:24:38 UTC  
> Updated at: 2022-03-08 17:52:37 UTC  
> Closed at: 2022-03-08 17:52:37 UTC  
> Url: https://github.com/boostorg/test/issues/320  

When calling BOOST_FIXTURE_TEST_SUITE with empty fixture object it causes a "stack overflow" exception in the destructor:  
**unknown location(0): fatal error: in "Bug/Dummy": stack overflow**  
and subsequent 2 blocks memory leak: "**Detected memory leaks! Dumping objects ->**"  
  
For full details see attached [bug.log](https://github.com/boostorg/test/files/7626380/bug.log) and [bug.cpp.txt](https://github.com/boostorg/test/files/7626382/bug.cpp.txt)  
  
Version checked was 1.68

---

## Comment 1

> Username: vkgl  
> Created at: 2021-12-02 15:40:21 UTC  
> Url: https://github.com/boostorg/test/issues/320#issuecomment-984744623  

It seems that I found the true reason for this problem. Fixture object names should be unique across different translation units, otherwise the objects that boost macros generate for the test suites and test cases will "overlap" during the linkage and improper destructors/methods will be called for the fixture objects. IMHO, this behavior should be reflected in the documentation somewhere.

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2022-02-27 01:30:38 UTC  
> Url: https://github.com/boostorg/test/issues/320#issuecomment-1052912523  

I am not sure to understand what the problem was: the code you posted here seems valid to me, but the problem was happening in another code?

---

## Comment 3

> Username: vkgl  
> Created at: 2022-02-27 13:31:51 UTC  
> Url: https://github.com/boostorg/test/issues/320#issuecomment-1053557244  

yes, this code was compiled within a set of other tests, other test had a fixture named Main but with some fields. During the linkage linker used constructor from one of the fixtures and destructor from another one. Because objects had different sizes the destructor improperly linked to another unit test caused buggy behavior. So the problem is not an empty test fixture but the name clash for separate compilation units.

---

## Comment 4

> Username: vkgl  
> Created at: 2022-02-27 13:37:40 UTC  
> Url: https://github.com/boostorg/test/issues/320#issuecomment-1053558615  

Let imagine there are 2 translation units:  
BOOST_FIXTURE_TEST_SUITE(Bug1, Main)  
and  
BOOST_FIXTURE_TEST_SUITE(Bug2, Main)  
and both of them have "struct Main"  
But the first struct is declared as "struct Main {}" and the second one as "struct Main { obtectType objectName; }"  
Then such different declarations are being defined as global objects by the "BOOST_FIXTURE_TEST_SUITE" macro.  
And of course the behavior will be incorrect.  
  
I hope that I clarified enough my post.

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2022-03-05 22:07:34 UTC  
> Url: https://github.com/boostorg/test/issues/320#issuecomment-1059838786  

Thanks for the info. I added a warning in the documentation about that error. In branch `topic/doc-update`

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2022-03-08 17:52:37 UTC  
> Url: https://github.com/boostorg/test/issues/320#issuecomment-1062046204  

Merged in https://github.com/boostorg/test/pull/337 rev https://github.com/boostorg/test/commit/b52b0ea6429c393485f8ea27ab5edd678823e65a.
