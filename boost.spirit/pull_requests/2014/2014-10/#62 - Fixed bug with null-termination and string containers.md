# #62 Fixed bug with null-termination and string containers. [Merged]

> Username: vtnerd  
> Created at: 2014-10-19 04:00:41 UTC  
> Updated at: 2014-10-19 15:55:07 UTC  
> Merged at: 2014-10-19 04:48:58 UTC  
> Closed at: 2014-10-19 04:48:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/62  

This patch is for the issue posted on the [mailing list](http://boost.2283326.n4.nabble.com/karma-lit-and-karma-string-failure-td4666182.html). The fix was to have std::string use the existing interface for STL containers. All karma v2 tests pass.

---
