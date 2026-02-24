# #592 - Test defines [Closed]

> Username: barendgehrels  
> Created at: 2019-05-22 10:03:17 UTC  
> Updated at: 2019-06-28 08:38:39 UTC  
> Closed at: 2019-06-28 08:38:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/592  

Hi,  
  
I've a small issue, we currently use 5 different defines to enable failing tests:  
  
```  
#ifdef BOOST_GEOMETRY_TEST_ENABLE_FAILING  
#ifdef BOOST_GEOMETRY_TEST_INCLUDE_FAILING_TESTS  
#ifdef BOOST_GEOMETRY_ENABLE_FAILING_TESTS  
#ifdef BOOST_GEOMETRY_INCLUDE_FAILING_TESTS  
#ifdef GEOMETRY_TEST_INCLUDE_FAILING_TESTS  
```  
  
Because for rescaling I'm busy with tests, and also adding/removing these every now and then, I would like to see them harmonized and can do that.  
  
I suppose it should now start with BOOST_GEOMETRY_TEST (because many others do so too, such as BOOST_GEOMETRY_TEST_PERMUTATIONS).  
  
Then **BOOST_GEOMETRY_TEST_INCLUDE_FAILING_TESTS** is the most clear to me. Though a bit long, and TEST is duplicate.  
  
I propose to change all into **BOOST_GEOMETRY_TEST_INCLUDE_FAILURES** which is clear and concise.  
  
Will I change that and include that in my next PR?

---

## Comment 1

> Username: awulkiew  
> Created at: 2019-05-22 10:51:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/592#issuecomment-494752717  

Good idea!   
  
Regarding the name. "Including" in c++ means something different. ENABLE/DISABLE is clear. Also removing the word would be fine too: BG_TEST_FAILING or BG_TEST_FAILURES.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2019-06-28 08:38:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/592#issuecomment-506653909  

Closing issue, it is merged already a month ago
