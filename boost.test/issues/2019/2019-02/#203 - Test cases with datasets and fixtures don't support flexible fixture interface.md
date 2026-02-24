# #203 - Test cases with datasets and fixtures don't support flexible fixture interface. [Closed]

> Username: dudamoos  
> Created at: 2019-02-08 04:40:13 UTC  
> Updated at: 2019-02-18 21:21:16 UTC  
> Closed at: 2019-02-18 21:20:51 UTC  
> Url: https://github.com/boostorg/test/issues/203  

I have a fixture that I use for several of my tests that requires using the `setup()` and `teardown()` methods offered by the flexible fixture interface. I use `BOOST_FIXTURE_TEST_CASE` to declare my test cases so I can use members of this fixture class in my test code. This works fine when I use it for normal test cases, but when I try to use it for test cases with datasets (declared via `BOOST_DATA_TEST_CASE_F`), the `setup()` and `teardown()` methods don't get called, causing the test cases to crash. Looking at the code, I notice that the code to run test cases with datasets doesn't call `setup_conditional()` or `teardown_conditional()` like the normal fixture test case code does. That should be a trivial fix, and should help avoid surprises in the future.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-02-08 10:28:22 UTC  
> Url: https://github.com/boostorg/test/issues/203#issuecomment-461758541  

Yes, I am aware of this one :) thanks for the reminder!

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2019-02-10 19:25:34 UTC  
> Url: https://github.com/boostorg/test/issues/203#issuecomment-462163300  

In branch `topic/GH-203-dataset-new-fixtures` if you want to give a try.

---

## Comment 3

> Username: dudamoos  
> Created at: 2019-02-13 00:58:09 UTC  
> Url: https://github.com/boostorg/test/issues/203#issuecomment-463009133  

That fix does the trick. I'll work on getting everyone here using an upgraded or patched version. Thanks!
