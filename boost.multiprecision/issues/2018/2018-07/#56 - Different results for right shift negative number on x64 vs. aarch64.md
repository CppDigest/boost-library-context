# #56 - Different results for right shift negative number on x64 vs. aarch64 [Closed]

> Username: guidovranken  
> Created at: 2018-07-29 01:50:34 UTC  
> Updated at: 2018-07-29 20:00:17 UTC  
> Closed at: 2018-07-29 20:00:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/56  

```  
std::cout << (boost::multiprecision::cpp_int(-777777777) >> 1) << std::endl;  
```  
  
Result is -388888889 on aarch and -388888888 on x64.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-07-29 16:04:04 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/56#issuecomment-408687563  

I know nothing about aarch, but I see -388888889 with both ubuntu x64 and msvc.  Or perhaps -388888888 was on aarch in which case that would be a bug?

---

## Comment 2

> Username: guidovranken  
> Created at: 2018-07-29 20:00:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/56#issuecomment-408701891  

Never mind, stock Ubuntu Boost shows -388888888 but Boost 1.67.0 shows -388888889.
