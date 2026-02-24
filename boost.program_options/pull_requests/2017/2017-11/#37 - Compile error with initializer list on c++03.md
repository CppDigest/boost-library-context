# #37 Compile error with initializer list on c++03 [Merged]

> Username: Flast  
> Created at: 2017-11-15 00:48:35 UTC  
> Updated at: 2018-03-08 04:59:45 UTC  
> Merged at: 2018-03-06 19:35:29 UTC  
> Closed at: 2018-03-06 19:35:29 UTC  
> Url: https://github.com/boostorg/program_options/pull/37  

Some regression failures caused by list initialization, see [exception_txt_test / clang-gnu-linux-4.0~gnu++98](http://www.boost.org/development/tests/develop/developer/output/Flast-FreeBSD11-boost-bin-v2-libs-program_options-test-exception_txt_test-test-clang-gnu-linux-4-0~gnu++98-debug-link-static-threadapi-pthread.html).

---

## Comment 1

> Username: eyalroz  
> Created_at: 2018-03-06 15:34:13 UTC  
> Updated_at: 2018-03-06 15:35:23 UTC  
> Url: https://github.com/boostorg/program_options/pull/37#issuecomment-370820868  

@Flast: The link in the comment here is dead.   
  
Anyway, this has got vote, as in my own PR #40 (about another issue) I did something similar to get rid of the compiler warning.

---
