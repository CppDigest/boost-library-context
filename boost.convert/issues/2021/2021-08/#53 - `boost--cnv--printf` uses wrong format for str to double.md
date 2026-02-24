# #53 - `boost::cnv::printf` uses wrong format for str to double [Closed]

> Username: dvirtz  
> Created at: 2021-08-21 21:00:23 UTC  
> Updated at: 2022-04-14 21:38:00 UTC  
> Closed at: 2022-04-14 21:38:00 UTC  
> Url: https://github.com/boostorg/convert/issues/53  

The correct `sscanf` format for `double` is `%lf` while current code uses `%f`:  
https://github.com/boostorg/convert/blob/2eec9560206a8490d81732cf685de3bc383265ef/include/boost/convert/printf.hpp#L75  
  
This results in wrong value when converting a string to `double`:  
```cpp  
fmt::print("{}\n", boost::convert<double>("3.14", cnv).value()); // prints 5.32861326e-315  
```  
  
https://gcc.godbolt.org/z/q1f1PK1cM

---

## Comment 1

> Username: yet-another-user  
> Created at: 2021-08-21 22:08:28 UTC  
> Url: https://github.com/boostorg/convert/issues/53#issuecomment-903182049  

Indeed. Thank you for picking it up. Much appreciated. Fixed in the develop branch. Should make it to the master branch the the next release. Tnx again.
