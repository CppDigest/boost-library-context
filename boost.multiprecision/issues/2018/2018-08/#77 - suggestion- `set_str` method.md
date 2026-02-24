# #77 - suggestion: `set_str` method [Closed]

> Username: ofloveandhate  
> Created at: 2018-08-19 05:33:02 UTC  
> Updated at: 2018-08-19 15:52:07 UTC  
> Closed at: 2018-08-19 08:05:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/77  

i think it would be swell if there were a method to set an existing number from a string, at its current precision, and an overload that lets me say what the precision is supposed to be.  
  
e.g.  
```  
mpfr_float.default_precision(50);  
mpfr_float a;  
a.set_str("1234"); // leaves precision of a alone, at 50  
a.set_str("1e-30",30); // a will now have precision 30  
```  
🎐

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-08-19 08:05:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/77#issuecomment-414111139  

I'll deal with this one first since it's an easy one, sort of  ;)  
  
Basic assignment is taken care of with  
  
```  
a.assign("123");  
```  
  
same for any type which is only explicitly convertible to the number type, for implicitly convertible types we obviously have operator=.  
  
There is currently no multi-argument assign for precision changing, which probably brings us back to the  
```  
a = std::move(mpfr_float("123", 100));  
```  
idiom.

---

## Comment 2

> Username: ofloveandhate  
> Created at: 2018-08-19 15:52:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/77#issuecomment-414136715  

excellent, thanks so much!
