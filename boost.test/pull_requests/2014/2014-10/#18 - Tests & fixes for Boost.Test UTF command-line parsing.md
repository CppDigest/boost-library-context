# #18 Tests & fixes for Boost.Test UTF command-line parsing. [Closed]

> Username: jurko-gospodnetic  
> Created at: 2014-10-23 20:27:33 UTC  
> Updated at: 2015-09-07 05:53:28 UTC  
> Closed at: 2015-09-07 05:53:28 UTC  
> Url: https://github.com/boostorg/test/pull/18  

UTF seems to have some serious issues with its command-line parsing. :-(  
  
This pull request adds a test module for it, and fixes one of the problems - a segfault that occurs if you attempt to pass it an argument with a leading embedded space character or an empty string as the initial but not only argument.  
  
There are other problems with this command-line handling, but I'll add failing tests for some of them as a separate pull request based on this one.  
  
Best regards,  
  Jurko Gospodnetić

---

## Comment 1

> Username: rogeeff  
> Created_at: 2015-09-07 05:53:28 UTC  
> Url: https://github.com/boostorg/test/pull/18#issuecomment-138194848  

I am completely rewriting CLA support for next release. It'll address lots of issues including these.

---
