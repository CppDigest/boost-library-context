# #21 - Assertion in boost::json::detail::pow10(int) [Closed]

> Username: pauldreik  
> Created at: 2019-12-04 20:38:50 UTC  
> Updated at: 2020-08-22 15:51:28 UTC  
> Closed at: 2020-08-22 15:51:28 UTC  
> Url: https://github.com/boostorg/json/issues/21  

This was already reported in https://github.com/vinniefalco/json/issues/13#issuecomment-560517119, but [here is a minimized version](https://github.com/pauldreik/json/blob/paul/fuzz/fuzzing/old_crashes/crash_01.json) and I think it is good to have it in a separate issue.  
  
I is the string 0.00....... with a lot of zeros following. Here it is, base 64 encoded:  
```  
MC4wMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAw  
MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAw  
MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAw  
MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAw  
MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAw  
MDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDA=  
```  
  
Running it through the fuzzer gives the following output:  
```sh  
paul@tonfisk:~/code/delaktig/boost.json/fuzzing$ ./fuzzer old_crashes/crash_01.json   
INFO: Seed: 2483239189  
INFO: Loaded 1 modules   (1092 inline 8-bit counters): 1092 [0x7d21a0, 0x7d25e4),   
INFO: Loaded 1 PC tables (1092 PCs): 1092 [0x5a1598,0x5a59d8),   
./fuzzer: Running 1 inputs 1 time(s) each.  
Running: old_crashes/crash_01.json  
fuzzer: ../include/boost/json/detail/impl/number.ipp:96: double boost::json::detail::pow10(int): Assertion `exp >= 0 && exp < 618' failed.  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-12-04 20:41:01 UTC  
> Url: https://github.com/boostorg/json/issues/21#issuecomment-561829703  

\sigh the treatment of floating point numbers is my shame...

---

## Comment 2

> Username: pauldreik  
> Created at: 2019-12-04 20:44:02 UTC  
> Url: https://github.com/boostorg/json/issues/21#issuecomment-561830742  

Well it is incredibly difficult to get right :-)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-12-04 20:44:42 UTC  
> Url: https://github.com/boostorg/json/issues/21#issuecomment-561831005  

It really is difficult, and unexpectedly so because..aren't computers supposed to be GOOD at math???

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-12-04 20:49:18 UTC  
> Url: https://github.com/boostorg/json/issues/21#issuecomment-561832663  

Promising advances in the field of string to double conversion:  
https://github.com/ulfjack/ryu/commits/parsing/ryu/s2d.c

---

## Comment 5

> Username: pauldreik  
> Created at: 2020-08-22 15:51:28 UTC  
> Url: https://github.com/boostorg/json/issues/21#issuecomment-678656891  

This bug has been obsoleted by refactoring during the last six months, closing.
