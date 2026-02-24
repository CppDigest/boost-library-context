# #9 Add testing for implicit_values and non-consuming of separate tokens. [Closed]

> Username: mjdecker  
> Created at: 2014-12-30 17:38:47 UTC  
> Updated at: 2014-12-30 23:11:07 UTC  
> Closed at: 2014-12-30 23:05:22 UTC  
> Url: https://github.com/boostorg/program_options/pull/9  

The testing did not really provide support for implicit_values or positional arguments.  So, I added this.  
  
Instead of introducing a new character for the syntax and since `?` and `=` had the same meaning, I changed `?` to be used for implicit_values (as in regular expressions it is 0 or 1).  All previous uses of `?` were changed to `=`.  
  
To make sure that positional arguments were not being consumed, positional_object_description is set up only for implicit_values.  The test_cmd code also needed to be slightly modified so positional arguments were taken as part of the previous option as position != -1.  
  
Feel free to change anything.

---

## Comment 1

> Username: vprus  
> Created_at: 2014-12-30 23:05:22 UTC  
> Url: https://github.com/boostorg/program_options/pull/9#issuecomment-68408693  

Michael,  
  
thanks for providing the testcase! I've checked it it, only adding a (size_t) case in one place to silence a warning.

---
