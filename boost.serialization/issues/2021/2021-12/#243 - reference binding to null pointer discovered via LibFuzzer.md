# #243 - reference binding to null pointer discovered via LibFuzzer [Open]

> Username: swirsz  
> Created at: 2021-12-24 17:44:49 UTC  
> Updated at: 2022-01-03 00:23:53 UTC  
> Url: https://github.com/boostorg/serialization/issues/243  

Bug detected via Libfuzzer & UndefinedBehaviorSanitizer.  
  
./cold-outputs_fuzz_tests crash-c1c.txt  
  
/src/monero/boost_1_70_0/boost/serialization/singleton.hpp:181:13: runtime error: reference binding to null pointer of type 'const boost::archive::detail::extra_detail::mapboost::archive::portable_binary_iarchive'  
SUMMARY: UndefinedBehaviorSanitizer  
  
[crash-c1c.txt](https://github.com/boostorg/boost/files/7774935/crash-c1c.txt)

---

## Comment 1

> Username: robertramey  
> Created at: 2022-01-03 00:23:53 UTC  
> Url: https://github.com/boostorg/serialization/issues/243#issuecomment-1003803042  

could you boil this down to a specific test case?
