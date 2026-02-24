# #233 - BOOST_AUTO_TEST_CASE_TEMPLATE with mpl that contains mutiple parametered templates causes unexecutable test cases because of comma [Closed]

> Username: martopad  
> Created at: 2019-10-27 01:34:46 UTC  
> Updated at: 2019-11-03 10:35:57 UTC  
> Closed at: 2019-11-03 10:35:45 UTC  
> Url: https://github.com/boostorg/test/issues/233  

Im currently porting a code base from boost 1.55 to 1.64.  
  
I am now having an issue with test cases that use BOOST_AUTO_TEST_CASE_TEMPLATE.  
  
Now, we are using this by feeding it with an mpl::vector of multiple parametered templates:  
```  
mpl::vector mytesttypes <   
    mytemplate<int,true>,  
    mytemplate<int,false> >;  
```  
previously these test cases are working because boost is using the mangled equivalent of these templates as test case names. But when i ported to boost 1.64, it now uses their demangled equivalent. As a result they now have commas in their test case names. And boost cannot execute these test cases anymore, because of it. The test cases are still registered and printed when searched for, but cannot be executed.   
  
Is this still a valid usage. Because looking at BOOST_AUTO_TEST_CASE_TEMPLATE usage example at boost 1.64 website, I think this is still a valid usage because the requirement is to use an mpl container.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-10-27 13:33:35 UTC  
> Url: https://github.com/boostorg/test/issues/233#issuecomment-546695097  

Hi,  
Thanks for the report. I believe this is related to #223 but to make sure, when you say:  
  
> And boost cannot execute these test cases anymore, because of it.  
  
do you mean executed from the command line? There is a workaround in #223 that might work for you if this is the case. If you mean that the tests are completely discarded even when not passed to the command line argument, I have not experienced this and it would mean a hard bug.  
  
Thanks!

---

## Comment 2

> Username: martopad  
> Created at: 2019-10-27 13:48:12 UTC  
> Updated at: 2019-10-27 15:35:37 UTC  
> Url: https://github.com/boostorg/test/issues/233#issuecomment-546696351  

Yes executed from the command line.  
  
Yes it is similar to #223, albeit different errors being printed. In my case "Test case not found" (but it is still being registered and i can search for it)   
  
Hmmmmm i dont quiet get the workarounds proposed. As I see it the workaround involves modifying the test case name before test-case registration phase?

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2019-10-27 13:52:29 UTC  
> Url: https://github.com/boostorg/test/issues/233#issuecomment-546696725  

Thanks for the quick feedback, the workaround is more about the filter passed on the command line, something like   
  
```  
-t "mytemplate<int,*"  
```  
  
but I doubt it works well, especially in your case. I am working on a fix, I believe I can make it for the upcoming 1.72 release.

---

## Comment 4

> Username: martopad  
> Created at: 2019-10-27 14:05:30 UTC  
> Updated at: 2019-10-27 14:19:29 UTC  
> Url: https://github.com/boostorg/test/issues/233#issuecomment-546697809  

Thank you also for the quick feedback. I should have raised my issue here first not on other platforms XD.  
  
hmmmm, i have only tried  
  
```  
-t "mytemplate*"  
```  
  
But if the workaround works, i would need to make modifications in our custom test runner.... But i doubt it would work.  
  
Since upgrading past this boost version is not an option(probably for a long time), looks like I will be doing a workaround myself... Is there a way that boost test will use the mangled equivalent of these templates as their test case names? just to avoid the comma issue.

---

## Comment 5

> Username: martopad  
> Created at: 2019-10-27 14:51:56 UTC  
> Url: https://github.com/boostorg/test/issues/233#issuecomment-546701868  

Or rather, Is there a way that i could "hijack" the test registration process in order to control the test case name?

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2019-10-27 15:00:51 UTC  
> Url: https://github.com/boostorg/test/issues/233#issuecomment-546702606  

Like this, it is a bit difficult to answer your question. The logic is done in the function `normalize_test_case_name` in the file `test_tree.ipp`, although I am not even sure that 1.64 has this already or the logic is factored in some other function.   
  
If you show me pieces of your custom test runner, maybe I can think of something.

---

## Comment 7

> Username: martopad  
> Created at: 2019-10-27 15:18:06 UTC  
> Updated at: 2019-10-27 15:34:01 UTC  
> Url: https://github.com/boostorg/test/issues/233#issuecomment-546704085  

In my findings a few days ago, this was the code that is responsible for determining the testcase name inside the angled brace. As i remember this was in a header file, looks like the logic your referring to does not exist yet in 1.64  
  
```  
#if !defined(BOOST_NO_TYPEID) && !defined(BOOST_NO_RTTI)  
        full_name += boost::core::demangle(typeid(TestType).name()); // same as execution_monitor.ipp  
#else  
        full_name += BOOST_CURRENT_FUNCTION;  
#endif  
```  
  
I have tried adding BOOST_NO_TYPEID, and BOOST_NO_RTTI in my build flags but to no avail still the same result (test case name with commas in them).  
  
Hmmmm Ill think about it first(Ill give an update in a few days) the information i got from this discussion is BIG! Thank you so much for the help!

---

## Comment 8

> Username: raffienficiaud  
> Created at: 2019-10-27 15:47:25 UTC  
> Url: https://github.com/boostorg/test/issues/233#issuecomment-546706871  

After the piece that you pointed, a new test case is created like this:  
  
```  
m_holder.m_test_cases.push_back( new test_case( ut_detail::normalize_test_case_name( full_name ),  
```  
  
The behaviour has changed a bit in [1.67](https://www.boost.org/doc/libs/1_71_0/libs/test/doc/html/boost_test/change_log.html#boost_test.change_log.boost_test_v3_7_boost_1_67) but the idea is the same (see `#12596 Sanitize metacharacters in test names`).   
  
In any case, the fix is dead easy and will be there for 1.72.

---

## Comment 9

> Username: martopad  
> Created at: 2019-10-28 05:58:27 UTC  
> Url: https://github.com/boostorg/test/issues/233#issuecomment-546802168  

[Additional Info]  
The error message being reported by boost was:  
```  
Test setup error: no test cases matching filter or all test cases were disabled  
Return code is: 200  
```

---

## Comment 10

> Username: martopad  
> Created at: 2019-10-29 13:02:57 UTC  
> Updated at: 2019-10-29 13:30:19 UTC  
> Url: https://github.com/boostorg/test/issues/233#issuecomment-547408279  

Greetings,   
  
Sad to say, our test runner is built in such a way that it needs to individually address each test case it discovers. It probably will need a lot of effort in order to make the proposed workaround work.   
  
I went with my second option. Which is "hijacking" BOOST_AUTO_TEST_CASE_TEMPLATE  to modify the code responsible for test case name generation. I did this by copying the implementation of BOOST_AUTO_TEST_CASE_TEMPLATE and modifying the code that is responsible for assigning the name inside the angled brace, in order to have no commas in them (in my case replace ',' with '_'). Then renaming the macro to something else. Now everything works :)  
```  
from  
// in test_case_template.hpp  
  
#if !defined(BOOST_NO_TYPEID) && !defined(BOOST_NO_RTTI)  
        full_name += boost::core::demangle(typeid(TestType).name()); // same as execution_monitor.ipp  
#else  
        full_name += BOOST_CURRENT_FUNCTION;  
#endif  
```  
  
```  
to  
full_name += boost::replace_all_copy<std::string>(boost::core::demangle(typeid(TestType).name()), ",", "_");  
```  
  
If you have suggestions on how i should improve this workaround, Im all ears. :)  
  
I have attached the file that I used in our code-base(I modified some code, especially names just for confidentiality)  
[HACKED_AUTO_TEST_CASE_TEMPLATE.txt](https://github.com/boostorg/test/files/3783608/HACKED_AUTO_TEST_CASE_TEMPLATE.txt)

---

## Comment 11

> Username: raffienficiaud  
> Created at: 2019-11-03 10:35:45 UTC  
> Url: https://github.com/boostorg/test/issues/233#issuecomment-549123604  

Hi,  
  
Thanks for the snippet. This is basically what I did with some extra stuff to normalize the test names across various OS/compilers.  
The changes are in 1c4533f7654d4c049cc6a34a43a5c10cde5bdd8b if you want to have a look and to back-port to your copy of boost.test. It is now in master and will be part of 1.72.  
  
I am closing now this issue as I consider it a duplicate of #223 which is now fixed. If you need any advise in migrating your test runner to new boost.test, I would be glad to help!  
  
Cheers,  
Raffi
