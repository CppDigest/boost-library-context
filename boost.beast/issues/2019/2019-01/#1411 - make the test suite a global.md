# #1411 - make the test suite a global [Closed]

> Username: vinniefalco  
> Created at: 2019-01-12 22:08:46 UTC  
> Updated at: 2019-01-18 02:21:39 UTC  
> Closed at: 2019-01-18 02:21:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1411  

Instead of a derived-class approach, the unit test suite should use a simple global. This way the test macros like `BEAST_EXPECT` do not need to be passed a reference to the suite, allowing for better composition.
