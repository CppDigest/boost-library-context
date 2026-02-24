# #17 Remove dependency on deprecated type_traits implementation details [Merged]

> Username: jhunold  
> Created at: 2015-05-27 19:03:43 UTC  
> Updated at: 2015-05-31 19:02:00 UTC  
> Merged at: 2015-05-31 19:01:50 UTC  
> Closed at: 2015-05-31 19:01:50 UTC  
> Url: https://github.com/boostorg/units/pull/17  

ice_or and and ice_and are workaround for non-conforming compilers.  
So just use operator || and && instead.  
  
Compiles and works with clang trunk + libc++ on linux.

---

## Comment 1

> Username: mkurdej  
> Created_at: 2015-05-28 09:07:53 UTC  
> Url: https://github.com/boostorg/units/pull/17#issuecomment-106242082  

Looks good. However I've tested it on Win8, MSVC2013, 32-bit & 64-bit and I get an error (but probably it's unrelated to your changes):  
  
```  
Entering test case "test_output_autoprefixed_quantity_name"  
unknown location(0): fatal error in "test_output_autoprefixed_quantity_name": unrecognized exception. Id: 0xc00002b5  
test_output.cpp(337): last checkpoint  
```  
  
in both 32-bit and 64-bit.

---

## Comment 2

> Username: jhunold  
> Created_at: 2015-05-28 17:17:04 UTC  
> Url: https://github.com/boostorg/units/pull/17#issuecomment-106500190  

@mkurdej  Thanks for testing. Will try with msvc-12.0 tomorrow. I've already tested with 14.0-RC, everything works.

---

## Comment 3

> Username: jhunold  
> Created_at: 2015-05-28 19:16:03 UTC  
> Updated_at: 2015-05-28 19:17:55 UTC  
> Url: https://github.com/boostorg/units/pull/17#issuecomment-106570020  

@mkurdej Tested with msvc-9.0, 10.0, 11.0 and 12.0 in 32- and 64-bit, all tests pass.   
One exception is a failure in the regex for detecting quiet NaNs, but this is another topic.  
Will commit the patch on Sunday if no-one objects.

---
