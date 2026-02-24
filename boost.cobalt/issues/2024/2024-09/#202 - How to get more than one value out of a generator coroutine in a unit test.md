# #202 - How to get more than one value out of a generator coroutine in a unit test? [Closed]

> Username: LegalizeAdulthood  
> Created at: 2024-09-19 14:51:18 UTC  
> Updated at: 2024-09-19 15:27:25 UTC  
> Closed at: 2024-09-19 15:27:25 UTC  
> Url: https://github.com/boostorg/cobalt/issues/202  

In my [sample code](https://github.com/LegalizeAdulthood/cobalt-comics) to go with the video [Using Coroutines With Boost.Cobalt](https://www.youtube.com/watch?v=yElSdUqEvME), I was attempting to write unit tests around my generator coroutine.  Because the generators in Cobalt are eager, I was always able to get the first value out of the generator, but I wasn't able to get a second value out.  
  
I suspect this is user error on my part, not understanding exactly how Cobalt interacts with the executor that I created via boost.asio, immitating the steps in Cobalt's `run_main`.  I tried using `poll()`, `run()`, and `run_one()` on the executor, but I could never seem to get another invocation of my generator coroutine to happen.  
  
What am I doing wrong?  
  
Perhaps some examples demonstrating unit-testing of coroutines could be added to show how to unit-test coroutines under controlled circumstances.  
  
Thanks.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-09-19 14:55:10 UTC  
> Url: https://github.com/boostorg/cobalt/issues/202#issuecomment-2361244723  

Can you link the actual code? Your sample link points to a repo.

---

## Comment 2

> Username: LegalizeAdulthood  
> Created at: 2024-09-19 14:56:27 UTC  
> Url: https://github.com/boostorg/cobalt/issues/202#issuecomment-2361248981  

Look at `test-cobalt.cpp` in the `tests` folder of that repo

---

## Comment 3

> Username: LegalizeAdulthood  
> Created at: 2024-09-19 14:56:53 UTC  
> Url: https://github.com/boostorg/cobalt/issues/202#issuecomment-2361251225  

https://github.com/LegalizeAdulthood/cobalt-comics/blob/master/tests/test-cobalt.cpp

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2024-09-19 15:01:26 UTC  
> Url: https://github.com/boostorg/cobalt/issues/202#issuecomment-2361266151  

Ah got it. Well everything in cobalt is mean to be asynchronous, i.e. the generator is mean to be used with `co_await`.  You're calling it from a regular function.  
  
You should create a `cobalt::task` in your test and co_await the generator from there. You can run that task blocking with `cobalt::run` from the test function. E.g.:   
  
```  
cobalt::task<void> TestComicsCobalt_notReadyFromNoMatchingSquences_impl()  
{  
   MockDatabasePtr db{createMockDatabase()};  
    ParsedJson sequences("[]");  
    EXPECT_CALL(*db, getSequences()).WillOnce(Return(sequences.m_document));  
    boost::cobalt::generator coro{matches(db, comics::CreditField::SCRIPT, SCRIPT_NAME)};  
  
    const SearchResult value1 = co_await coro;  
    const auto value2 = co_await coro;  
}  
  
TEST_F(TestComicsCobalt, notReadyFromNoMatchingSquences)  
{  
     // go into async land here.  
     cobalt::run(TestComicsCobalt_notReadyFromNoMatchingSquences_impl());  
}  
```

---

## Comment 5

> Username: LegalizeAdulthood  
> Created at: 2024-09-19 15:05:46 UTC  
> Url: https://github.com/boostorg/cobalt/issues/202#issuecomment-2361280580  

I don't think it will work verbatim as you have it, but I'm trying something out.

---

## Comment 6

> Username: LegalizeAdulthood  
> Created at: 2024-09-19 15:13:27 UTC  
> Url: https://github.com/boostorg/cobalt/issues/202#issuecomment-2361306500  

Gtest is macro crazy/happy.  You can't have gtest macro invocations outside of a test case; yes, it's a dumb design decision, but that's how it is.  So I have to write a task that does the `co_await` to get the two values and return them to the caller so the assertions can be done in the test case.  I tried this:  
  
```  
boost::cobalt::task<std::vector<SearchResult>> twoValues()  
{  
    MockDatabasePtr db{createMockDatabase()};  
    ParsedJson issues{ISSUES};  
    ParsedJson sequences{SEQUENCES};  
    EXPECT_CALL(*db, getSequences()).WillOnce(Return(sequences.m_document));  
    EXPECT_CALL(*db, getIssues()).WillOnce(Return(issues.m_document));  
    boost::cobalt::generator coro{matches(db, comics::CreditField::SCRIPT, SCRIPT_NAME)};  
  
    const SearchResult match{co_await coro};  
    const SearchResult secondMatch{co_await coro};  
    co_return std::vector<SearchResult>{match,secondMatch};  
}  
  
TEST_F(TestComicsCobalt, readyFromFirstMatchOfMultiple)  
{  
    std::vector<SearchResult> result{boost::cobalt::run(twoValues)};  
}  
```  
but I get a compile error trying to compile the invocation of `run`:  
```  
1>D:\legalize\utahcpp\cobalt\cobalt-comics\tests\test-cobalt.cpp(238,53): error C2664: 'T boost::cobalt::run<std::vector<comics::cobalt::SearchResult,std::allocator<comics::cobalt::SearchResult>>>(boost::cobalt::task<std::vector<comics::cobalt::SearchResult,std::allocator<comics::cobalt::SearchResult>>>)': cannot convert argument 1 from 'boost::cobalt::task<std::vector<comics::cobalt::SearchResult,std::allocator<comics::cobalt::SearchResult>>> (__cdecl *)(void)' to 'boost::cobalt::task<std::vector<comics::cobalt::SearchResult,std::allocator<comics::cobalt::SearchResult>>>'  
1>D:\legalize\utahcpp\cobalt\cobalt-comics\tests\test-cobalt.cpp(238,53): error C2664:         with  
1>D:\legalize\utahcpp\cobalt\cobalt-comics\tests\test-cobalt.cpp(238,53): error C2664:         [  
1>D:\legalize\utahcpp\cobalt\cobalt-comics\tests\test-cobalt.cpp(238,53): error C2664:             T=std::vector<comics::cobalt::SearchResult,std::allocator<comics::cobalt::SearchResult>>  
1>D:\legalize\utahcpp\cobalt\cobalt-comics\tests\test-cobalt.cpp(238,53): error C2664:         ]  
1>    D:\legalize\utahcpp\cobalt\cobalt-comics\tests\test-cobalt.cpp(238,57):  
```  
  
I don't understand this error because the declaration of `run` is:  
```  
template<typename T>  
T run(task<T> t)  
```  
...which implies if the return type of run should be the same as the template argument to task.

---

## Comment 7

> Username: LegalizeAdulthood  
> Created at: 2024-09-19 15:15:32 UTC  
> Url: https://github.com/boostorg/cobalt/issues/202#issuecomment-2361313880  

Also, in case you didn't notice, the executor via boost.asio is being created in the fixture for the test case, lines 179-184  
  
https://github.com/LegalizeAdulthood/cobalt-comics/blob/master/tests/test-cobalt.cpp#L179

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2024-09-19 15:15:54 UTC  
> Url: https://github.com/boostorg/cobalt/issues/202#issuecomment-2361314811  

```cpp  
boost::cobalt::run(twoValues())  
```  
  
instead of   
  
```cpp  
boost::cobalt::run(twoValues)  
```

---

## Comment 9

> Username: LegalizeAdulthood  
> Created at: 2024-09-19 15:16:52 UTC  
> Url: https://github.com/boostorg/cobalt/issues/202#issuecomment-2361317076  

oh right, you don't get the return type instantiated for a coroutine until you 'contruct' it by calling the function.  Honestly the coroutine stuff in C++20 feels a little hacky `:)`

---

## Comment 10

> Username: klemens-morgenstern  
> Created at: 2024-09-19 15:18:23 UTC  
> Url: https://github.com/boostorg/cobalt/issues/202#issuecomment-2361320470  

It's a bit, but not as much as one first thinks. Coroutines are just inherently weird.  
  
I created the same thing for boost.test btw.: https://github.com/boostorg/cobalt/blob/develop/test/test.hpp

---

## Comment 11

> Username: LegalizeAdulthood  
> Created at: 2024-09-19 15:27:17 UTC  
> Url: https://github.com/boostorg/cobalt/issues/202#issuecomment-2361341146  

OK, with a little shuffling around to ensure proper lifetimes of test data, I end up with this:  
  
```  
boost::cobalt::task<std::vector<SearchResult>> twoValues(MockDatabasePtr db)  
{  
    boost::cobalt::generator coro{matches(db, comics::CreditField::SCRIPT, SCRIPT_NAME)};  
  
    const SearchResult match{co_await coro};  
    const SearchResult secondMatch{co_await coro};  
    co_return std::vector<SearchResult>{match,secondMatch};  
}  
  
TEST_F(TestComicsCobalt, readyFromFirstMatchOfMultiple)  
{  
    MockDatabasePtr db{createMockDatabase()};  
    ParsedJson issues{ISSUES};  
    ParsedJson sequences{SEQUENCES};  
    EXPECT_CALL(*db, getSequences()).WillOnce(Return(sequences.m_document));  
    EXPECT_CALL(*db, getIssues()).WillOnce(Return(issues.m_document));  
  
    const std::vector matches{run(twoValues(db))};  
  
    const SearchResult &match{matches[0]};  
    ASSERT_TRUE(match.has_value());  
    EXPECT_EQ("1", match.value().issue.at_key("issue number").get_string().value());  
    EXPECT_NE(std::string::npos, match.value().sequence.at_key("script").get_string().value().find(SCRIPT_NAME));  
    EXPECT_EQ("cover", match.value().sequence.at_key("type").get_string().value());  
    const SearchResult &secondMatch{matches[1]};  
    EXPECT_EQ("1", secondMatch.value().issue.at_key("issue number").get_string().value());  
    EXPECT_NE(std::string::npos, secondMatch.value().sequence.at_key("script").get_string().value().find(SCRIPT_NAME));  
    EXPECT_EQ("comic story", secondMatch.value().sequence.at_key("type").get_string().value());  
}  
```  
  
Thanks for your assistance!
