# #333 - Fuzzer CI failed to detect null passed to deallocate() [Closed]

> Username: pauldreik  
> Created at: 2020-09-14 05:53:20 UTC  
> Updated at: 2020-09-16 16:51:17 UTC  
> Closed at: 2020-09-16 16:51:17 UTC  
> Url: https://github.com/boostorg/json/issues/333  

This is a ticket to remember to improve the fuzzing setup.  
  
Fuzzing on arm64 by invoking fuzz.sh on a fresh repo immediately uncovered null being passed to deallocate (undefined behaviour). This was reported by me on slack and Vinnie fixed it almost instantly in https://github.com/CPPAlliance/json/commit/7c659533d05b40517d33ed6ff0f4e5df452ebb6f. For reference, this is the output (recreated on x64, see further down):  
  
```  
../include/boost/json/impl/value_stack.ipp:29:13: runtime error: null pointer passed as argument 1, which is declared to never be null  
/usr/bin/../lib/gcc/x86_64-linux-gnu/10/../../../../include/c++/10/memory_resource:103:20: note: nonnull attribute specified here  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../include/boost/json/impl/value_stack.ipp:29:13 in   
```  
  
The question is: why was this not detected by the existing github action fuzz job? If it failed to detect this, similar bugs easily detected by fuzzing could remain undetected.  
  
I tried it now on amd64 and the fuzzer catches it if -DBOOST_JSON_STACK_BUFFER_SIZE=256 is set.  
  
Vinnie suggested on slack to construct the parser object with various sizes of temporary buffer (none, 1 byte, 1kbyte etc). Also, using default storage, null_resource and possibly combinations of the above.  
  
These setting should be dynamically picked from the fuzz data to be as flexible as possible.

---

## Comment 1

> Username: pauldreik  
> Created at: 2020-09-15 20:05:37 UTC  
> Url: https://github.com/boostorg/json/issues/333#issuecomment-692949865  

@vinniefalco I made a prototype. Could you please have a look? I used the examples on https://develop.json.cpp.al/json/usage/parsing.html#json.usage.parsing.controlling_memory  
  
https://github.com/pauldreik/json/blob/pauldreik/fuzz_memory_strategies/fuzzing/fuzz_parser.cpp

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-09-15 20:57:46 UTC  
> Url: https://github.com/boostorg/json/issues/333#issuecomment-692975220  

That's pretty good :)
