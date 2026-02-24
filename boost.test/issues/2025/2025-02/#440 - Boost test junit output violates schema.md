# #440 - Boost test junit output violates schema [Open]

> Username: travisdowns  
> Created at: 2025-02-02 22:55:13 UTC  
> Updated at: 2025-02-02 22:55:57 UTC  
> Url: https://github.com/boostorg/test/issues/440  

When using the junit results formatter, the XML attributes like `time` use default float ostream formatting which uses scientific notation at times. However the junit schema says time is xs:decimal and scientific notation is not valid xs:decimal.   
  
I suggest that boost uses fixed formatting instead.   
  
Among other things this means boost test results fail to be parsed by bazel.
