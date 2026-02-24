# #582 - Classic: escape_char_parser_tests fails on ARM [Closed]

> Username: Kojoley  
> Created at: 2020-03-30 16:43:07 UTC  
> Updated at: 2020-04-02 18:21:47 UTC  
> Closed at: 2020-04-02 18:21:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/582  

https://www.boost.org/development/tests/develop/developer/output/teeks99-05-dc9-2a-64onarmv7l-boost-bin-v2-libs-spirit-classic-test-escape_char_parser_tests-test-clang-linux-9~c++2a-debug-address-model-64-threading-multi-visibility-hidden.html  
https://www.boost.org/development/tests/develop/developer/output/teeks99-05-dg8-2a-64onarmv7l-boost-bin-v2-libs-spirit-classic-test-escape_char_parser_tests-test-gcc-8~c++2a-debug-address-model-64-threading-multi-visibility-hidden.html  
  
```  
../libs/spirit/classic/test/escape_char_parser_tests.cpp(173): test '!parse(octmax, wlep[assign_a(wc)]).hit' failed in function 'int main()'  
../libs/spirit/classic/test/escape_char_parser_tests.cpp(186): test '!parse(hexmax, wlep[assign_a(wc)]).hit' failed in function 'int main()'  
2 errors detected.  
```

---

## Comment 1

> Username: Kojoley  
> Created at: 2020-04-01 21:24:00 UTC  
> Url: https://github.com/boostorg/spirit/issues/582#issuecomment-607496926  

Spend half an hour trying to reproduce the problem without noticing that the failure was on ARM platform.
