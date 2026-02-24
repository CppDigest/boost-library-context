# #167 - str() gives weird result with global locale [Closed]

> Username: dlaugt  
> Created at: 2019-11-19 12:53:19 UTC  
> Updated at: 2019-12-06 19:05:33 UTC  
> Closed at: 2019-12-06 19:05:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/167  

`str()` can give weird result mainly because it uses `boost::lexical_cast` and `stringstream` which are dependant of the global locale:  
```cpp  
locale::global (locale ("en-US"));  
boost::multiprecision::cpp_dec_float_50 d {"1234.56"};  
d.str ();  
// gives 1,23.456,000,  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-12-06 16:49:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/167#issuecomment-562649945  

Confirmed.  
  
There are enough (lazy on my part) uses of lexical_cast that a larger solution is required - though I will no doubt pinch some of your suggested fix.
